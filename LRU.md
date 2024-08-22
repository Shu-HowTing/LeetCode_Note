## LRU（Least Recently Used）

- **概念**：LRU 算法基于最近使用的原则，它认为最久未使用的数据最有可能是不会再被访问的。因此，当缓存满了需要替换数据时，LRU 会移除最近最少使用的数据。

- **实现**：通常使用双向链表和哈希表来实现。
  
  - 双向链表保存数据的访问顺序，最近访问的数据移动到链表的头部，链表尾部的数据是最久未使用的。
  - 哈希表用于快速查找数据的位置。

- **适用场景**：LRU 适用于那些具有时间局部性的数据访问模式，即最近使用的数据可能会在不久后再次使用。

- **优点**：实现相对简单，性能相对较高。

- **缺点**：它只考虑了数据的访问时间，而忽略了访问频率。如果某些数据虽然访问过一次但之后很少使用，LRU 也不会将它替换掉。

## LRU缓存的实现思路

1. **双向链表**：
   
   - 链表的头部保存最近使用的数据，尾部保存最久未使用的数据。
   - 每次访问某个数据时，将该数据移动到链表的头部。
   - 当缓存满了，需要淘汰数据时，删除链表尾部的数据。

2. **哈希表**：
   
   - 哈希表用于将缓存中的键映射到链表中的节点，支持O(1)时间复杂度的快速查找。
   - 通过哈希表，可以在O(1)时间内找到链表中对应的节点，并将其移动到链表头部。

## LRU缓存的核心操作

- **get(key)**：如果键存在于缓存中，则获取对应的值，并将该键移动到链表的头部；如果键不存在，返回-1。
- **put(key, value)**：如果键存在于缓存中，更新对应的值，并将该键移动到链表的头部；如果键不存在，插入新的键值对到链表头部，并在缓存满时，移除链表尾部的节点。

```py
class Node:
    def __init__(self, key, value):
        self.key = key
        self.value = value
        self.prev = None
        self.next = None

class LRUCache:
    def __init__(self, capacity: int):
        self.capacity = capacity
        self.cache = {}
        # 使用伪头部和伪尾部节点来避免空指针的处理
        self.head = Node(0, 0)
        self.tail = Node(0, 0)
        self.head.next = self.tail
        self.tail.prev = self.head

    def _remove(self, node: Node):
        """移除链表中的一个节点"""
        prev_node = node.prev
        next_node = node.next
        prev_node.next = next_node
        next_node.prev = prev_node

    def _add_to_head(self, node: Node):
        """将节点添加到链表头部"""
        node.next = self.head.next
        node.prev = self.head
        self.head.next.prev = node
        self.head.next = node

    def get(self, key: int) -> int:
        if key in self.cache:
            node = self.cache[key]
            self._remove(node)
            self._add_to_head(node)
            return node.value
        return -1

    def put(self, key: int, value: int) -> None:
        if key in self.cache:
            node = self.cache[key]
            node.value = value
            self._remove(node)
            self._add_to_head(node)
        else:
            if len(self.cache) >= self.capacity:
                # 移除链表尾部的节点（即最久未使用的节点）
                lru_node = self.tail.prev
                self._remove(lru_node)
                del self.cache[lru_node.key]
            new_node = Node(key, value)
            self.cache[key] = new_node
            self._add_to_head(new_node)
```
