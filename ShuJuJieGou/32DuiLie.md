```json
{
  "updated_at": "2020-07-12",
  "updated_by": "KelipuTe",
  "tags": "数据结构,Data Structure,线性表,List"
}
```

---

## 队列

队列（queue）是只允许**在一端进行插入操作，在另一端进行删除操作**的线性表。

队列是一种**先进先出**（First In First Out，FIFO）的线性表，简称 FIFO。允许插入的一端称为队尾，允许删除的一端称为队头。

队列可以使用顺序存储结构，也可以使用链式存储结构，链式存储结构也称为链队列。

队列的链式存储结构，其实就是单链表，只不过它只能尾进头出而已。

### 循环队列

使用顺序存储结构时，出队列的操作会伴随数据元素的移动，效率不高。这时可以换个思路，出队列时不移动元素而是移动队头。但是这就会出现，存储结构入队的一端的空间已经满了，但是出队的一段的空间是空闲的问题，称为假溢出。

循环队列可以解决假溢出的问题。入队的一端空间用完了，就从头开始。我们把队列的这种头尾相接的顺序存储结构称为循环队列。

循环队列的队头指针和队尾指针是可以一直增加的，在映射到队列结构上的时候通过取模运算，就可以得到对应的位置。