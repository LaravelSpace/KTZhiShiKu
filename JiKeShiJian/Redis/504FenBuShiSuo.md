## 分布式锁

Redis客户端可以通过加锁的方式，来控制并发写操作对共享数据的修改，从而保证数据的正确性。但是，Redis属于分布式系统，当有多个客户端需要争抢锁时，必须要保证，这把锁不能是某个客户端本地的锁。否则的话，其它客户端是无法访问这把锁的，当然也就不能获取这把锁了。

所以，在分布式系统中，当有多个客户端需要获取锁时，需要分布式锁。此时，锁是保存在一个共享存储系统中的，可以被多个客户端共享访问和获取。Redis本身可以被多个客户端共享访问，正好就是一个共享存储系统，可以用来保存分布式锁。而且Redis的读写性能高，可以应对高并发的锁操作场景。

### 单机上的锁和分布式锁的联系与区别

对于在单机上运行的多线程程序来说，锁本身可以用一个变量表示。变量值为0时，表示没有线程获取锁；变量值为1时，表示已经有线程获取到锁了。一个线程调用加锁操作，其实就是检查锁变量值是否为0。如果是0，就把锁的变量值设置为1，表示获取到锁，如果不是0，就返回错误信息，表示加锁失败，已经有别的线程获取到锁了。而一个线程调用释放锁操作，其实就是将锁变量的值置为0，以便其它线程可以来获取锁。

和单机上的锁类似，分布式锁同样可以用一个变量来实现。客户端加锁和释放锁的操作逻辑，也和单机上的加锁和释放锁操作逻辑一致：加锁时同样需要判断锁变量的值，根据锁变量值来判断能否加锁成功；释放锁时需要把锁变量值设置为0，表明客户端不再持有锁。

但是，和线程在单机上操作锁不同的是，在分布式场景下，锁变量需要由一个共享存储系统来维护，只有这样，多个客户端才可以通过访问共享存储系统来访问锁变量。相应的，加锁和释放锁的操作就变成了读取、判断和设置共享存储系统中的锁变量值。

这样一来，就可以得出实现分布式锁的两个要求。要求一：分布式锁的加锁和释放锁的过程，涉及多个操作。所以，在实现分布式锁时，需要保证这些锁操作的原子性；要求二：共享存储系统保存了锁变量，如果共享存储系统发生故障或宕机，那么客户端也就无法进行锁操作了。在实现分布式锁时，需要考虑保证共享存储系统的可靠性，进而保证锁的可靠性。

### 基于单个Redis节点实现分布式锁

作为分布式锁实现过程中的共享存储系统，Redis可以使用键值对来保存锁变量，再接收和处理不同客户端发送的加锁和释放锁的操作请求。可以赋予锁变量一个变量名，把这个变量名作为键值对的键，而锁变量的值，则是键值对的值，这样一来，Redis就能保存锁变量了，客户端也就可以通过Redis的命令操作来实现锁操作。

![](E:\GongZuoQu\ZhiShiKu\TuPian\JiKeShiJian\Redis\FenBuShiSuo_img02.jpg)

![](E:\GongZuoQu\ZhiShiKu\TuPian\JiKeShiJian\Redis\FenBuShiSuo_img04.jpg)

加锁包含了三个操作（读取锁变量、判断锁变量值以及把锁变量值设置为1），而这三个操作在执行时需要保证原子性。要想保证操作的原子性，有两种通用的方法，分别是使用Redis的单命令操作和使用Lua脚本。

在分布式加锁场景下，首先是SETNX命令，它用于设置键值对的值。具体来说，就是这个命令在执行时会判断键值对是否存在，如果不存在，就设置键值对的值，如果存在，就不做任何设置。对于释放锁操作来说，可以在执行完业务逻辑后，使用DEL命令删除锁变量。不过，使用SETNX和DEL命令组合实现分布锁，存在两个潜在的风险。

第一个风险是，假如某个客户端在执行了SETNX命令、加锁之后，紧接着却在操作共享数据时发生了异常，结果一直没有执行最后的DEL命令释放锁。因此，锁就一直被这个客户端持有，其它客户端无法拿到锁，也无法访问共享数据和执行后续操作，这会给业务应用带来影响。

针对这个问题，一个有效的解决方法是，给锁变量设置一个过期时间。这样一来，即使持有锁的客户端发生了异常，无法主动地释放锁，Redis也会根据锁变量的过期时间，在锁变量过期后，把它删除。其它客户端在锁变量过期后，就可以重新请求加锁，这就不会出现无法加锁的问题了。

第二个风险。如果客户端A执行了SETNX命令加锁后，假设客户端B执行了DEL命令释放锁，此时，客户端A的锁就被误释放了。如果客户端C正好也在申请加锁，就可以成功获得锁，进而开始操作共享数据。这样一来，客户端A和C同时在对共享数据进行操作，数据就会被修改错误，这也是业务层不能接受的。为了应对这个问题，就需要能区分来自不同客户端的锁操作。

这个问题，可以在锁变量的值上想想办法。在使用SETNX命令进行加锁的方法中，通过把锁变量值设置为1或0，表示是否加锁成功。1和0只有两种状态，无法表示究竟是哪个客户端进行的锁操作。所以，在加锁操作时，可以让每个客户端给锁变量设置一个唯一值，这里的唯一值就可以用来标识当前操作的客户端。在释放锁操作时，客户端需要判断，当前锁变量的值是否和自己的唯一标识相等，只有在相等的情况下，才能释放锁。这样一来，就不会出现误释放锁的问题了。

在说SETNX命令的时候提到，对于不存在的键值对，它会先创建再设置值（也就是不存在即设置），为了能达到和SETNX命令一样的效果，Redis给SET命令提供了类似的选项NX，用来实现不存在即设置。如果使用了NX选项，SET命令只有在键值对不存在时，才会进行设置，否则不做赋值操作。此外，SET命令在执行时还可以带上EX或PX选项，用来设置键值对的过期时间。

### 基于多个Redis节点实现高可靠的分布式锁

当要实现高可靠的分布式锁时，就不能只依赖单个的命令操作了，需要按照一定的步骤和规则进行加解锁操作，否则，就可能会出现锁无法工作的情况。一定的步骤和规则，其实就是分布式锁的算法。为了避免Redis实例故障而导致的锁无法工作的问题，Redis的开发者Antirez提出了分布式锁算法Redlock。

Redlock算法的基本思路，是让客户端和多个独立的Redis实例依次请求加锁，如果客户端能够和半数以上的实例成功地完成加锁操作，那么我们就认为，客户端成功地获得分布式锁了，否则加锁失败。这样一来，即使有单个Redis实例发生故障，因为锁变量在其它实例上也有保存，所以，客户端仍然可以正常地进行锁操作，锁变量并不会丢失。

Redlock算法的实现需要有N个独立的Redis实例。接下来，可以分成3步来完成加锁操作。

第一步是，客户端获取当前时间。

第二步是，客户端按顺序依次向N个Redis实例执行加锁操作。这里的加锁操作和在单实例上执行的加锁操作一样，使用SET命令，带上NX，EX/PX选项，以及带上客户端的唯一标识。当然，如果某个Redis实例发生故障了，为了保证在这种情况下，Redlock算法能够继续运行，我们需要给加锁操作设置一个超时时间。

如果客户端在和一个Redis实例请求加锁时，一直到超时都没有成功，那么此时，客户端会和下一个Redis实例继续请求加锁。加锁操作的超时时间需要远远地小于锁的有效时间，一般也就是设置为几十毫秒。

第三步是，一旦客户端完成了和所有Redis实例的加锁操作，客户端就要计算整个加锁过程的总耗时。客户端只有在满足下面的这两个条件时，才能认为是加锁成功。条件一：客户端从超过半数（大于等于N/2+1）的Redis实例上成功获取到了锁；条件二：客户端获取锁的总耗时没有超过锁的有效时间。

在满足了这两个条件后，我们需要重新计算这把锁的有效时间，计算的结果是锁的最初有效时间减去客户端为获取锁的总耗时。如果锁的有效时间已经来不及完成共享数据的操作了，我们可以释放锁，以免出现还没完成数据操作，锁就过期了的情况。当然，如果客户端在和所有实例执行完加锁操作后，没能同时满足这两个条件，那么，客户端向所有Redis节点发起释放锁的操作。

在Redlock算法中，释放锁的操作和在单实例上释放锁的操作一样，只要执行释放锁的Lua脚本就可以了。这样一来，只要N个Redis实例中的半数以上实例能正常工作，就能保证分布式锁的正常工作了。所以，在实际的业务应用中，如果想要提升分布式锁的可靠性，就可以通过Redlock算法来实现。