## 编码：隐匿在计算机软硬件背后的的语言

[美]佩措尔德，电子工业出版社出版，2012年10月出版，2012年10月印刷。

### 冯·诺依曼体系结构

冯·诺依曼结构也称普林斯顿结构。数学家冯·诺依曼提出了计算机制造的三个基本原则，即采用二进制逻辑、程序存储执行以及计算机由五个部分组成（运算器、控制器、存储器、输入设备、输出设备），这套理论被称为冯·诺依曼体系结构。

这种结构特点是“程序存储，共享数据，顺序执行”，需要 CPU 从存储器取出指令和数据进行相应的计算。

主要特点有：
（1）单处理机结构，机器以运算器为中心；
（2）采用程序存储思想；
（3）指令和数据一样可以参与运算；
（4） 数据以二进制表示；
（5）将软件和硬件完全分离；
（6） 指令由操作码和操作数组成；
（7）指令顺序执行；

局限

CPU 与共享存储器间的信息交换的速度成为影响系统性能的主要因素，而信息交换速度的提高又受制于存储元件的速度、存储器的性能和结构等诸多条件。

传统冯·诺依曼计算机体系结构的存储程序方式造成了系统对存储器的依赖，CPU 访问存储器的速度制约了系统运行的速度。集成 电路 IC 芯片的技术水平决定了存储器及其他硬件的性能。为了提高硬件的性能， 以英特尔公司为代表的芯片制造企业在集成电路生产方面做出了极大的努力，且获得了巨大的技术成果。 现在每隔 18 个 月 IC 的集成度翻一倍，性能也提升一倍，产品价格降低一半，这就是所谓的“摩尔定律”。 这个规律已经持续了40 多年，估计还将延续若干年。然而，电子产品面临的二个基本限制是客观存在的：光的速度和材料的原子特性。首先，信息传播的速度最终将取决于电子流动的速度，电子信号在元件和导线里流动会产生时间延迟，频率过高会造成信号畸变，所以元件的速度不可能无限的提高直至达到光速。第二，计算机的电子信号存储在以硅晶体材料为代表晶体管上，集成度的提高在于晶体管变小，但是晶体管不可能小于一个硅原子的体积。 随着半导体技术逐渐逼近硅工艺尺寸极限，摩尔定律原导出的规律将不再适用。

对冯·诺依曼计算机体系结构缺陷的分析：
（1）指令和数据存储在同一个存储器中，形成系统对存储器的过分依赖。如果储存器件的发展受阻，系统的发展也将受阻。
（2）指令在存储器中按其执行顺序存放，由指令计数器PC指明要执行的指令所在的单元地址。 然后取出指令执行操作任务。所以指令的执行是串行。影响了系统执行的速度。
（3）存储器是按地址访问的线性编址，按顺序排列的地址访问，利 于存储和执行的机器语言指令，适用于作数值计算。但是高级语言表示的存储器则是一组有名字的变量，按名字调用变量，不按地址访问。机器语言同高级语言在语义上存在很大的间隔， 称之为冯·诺依曼语 义间隔。消除语义间隔成了计算机发展面临的一大难题。
（4）冯·诺依曼体系结构计算机是为算术和逻辑运算而诞生的，目前在数值处理方面已经到达较高的速度和精度，而非数值处理应用领域发展缓慢，需要在体系结构方面有重大的突破。
（5）传统的冯·诺依曼型结构属于控制驱动方式。它是执行指令代码对数值代码进行处理，只要指令明确，输入数据准确，启动程序后自动运行而且结果是预期的。一旦指令和数据有错误，机器不会主动修改指令并完善程序。而人类生活中有许多信息是模糊的，事件的发生、发展和结果是不能预期的，现代计算机的智能是无法应对如此复杂任务的。

---

| 参考来源                                                     |
| ------------------------------------------------------------ |
| [百度文库-冯·诺依曼结构](https://baike.baidu.com/item/%E5%86%AF%C2%B7%E8%AF%BA%E4%BE%9D%E6%9B%BC%E7%BB%93%E6%9E%84/9536784?fr=aladdin) |

