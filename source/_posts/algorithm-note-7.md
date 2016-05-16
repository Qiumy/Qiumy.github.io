layout: post
title: 算法导论笔记（七）—— Chap 10 基本数据结构
comment: true
date: 2016-05-16 10:10:13
tags: [算法,算法导论,技术,笔记] 
---

# 基本数据结构

动态集合的元素：每个元素都有一个对象来表示，并可以假定对象中的一个属性为标识关键字。对象可能包含卫星数据。

动态集合上的操作：简单返回有关集合信息的查询操作和改变集合的修改操作。一些标准的操作如下：search, insert, delete, minimum, maxinum, successor, predecessor。

<!-- more -->

## 栈

栈实现的是一种后进先出的策略，被删除的是最近插入的元素。下面使用数组实现栈的结构。

```python
class Stack:
	def __init__(self, size = 10):
		self.stack = []
		self.size = size
		self.top = -1

	def setSize(self, size):
		self.size = size

	def isEmpty(self):
		if self.top == -1:
			return True
		else:
			return False

	def top(self):
		if self.isEmpty():
			print "error: stack is empty"
			return
		else:
			return self.stack[self.top]

	def pop(self):
		if self.isEmpty():
			print "error: stack is empty"
			return
		else:
			self.top -= 1
			return self.stack.pop()

	def isFull(self):
		if self.top+1 == self.size:
			return True
		else:
			return False

	def push(self, x):
		if self.isFull():
			print "error: stack is full"
			return
		else:
			self.top += 1
			self.stack.append(x)

	def show(self):
		print self.stack

if __name__ == '__main__':
	s = Stack(3)
	s.push(1)
	s.push(2)
	s.push(3)
	s.show()
	s.pop()
	s.show()
	s.push(6)
	s.push(4)
```

## 队列

队列实现的是一种先进先出的策略：被删去的总是在集合中存在时间最长的那个元素。

```python
class Queue:
	def __init__(self, size=10):
		self.queue = []
		self.size = size
		self.head = 0
		self.tail = 0

	def isEmpty(self):
		return self.tail == 0

	def isFull(self):
		if (self.tail - self.head) == self.size:
			return True
		else:
			return False

	def frist(self):
		if self.isEmpty():
			print "error: queue is empty"
			return
		else:
			return self.queue[self.head]

	def last(self):
		if self.isEmpty():
			print "error: queue is empty"
			return
		else:
			return self.queue[self.tail-1]

	def enqueue(self, x):
		if self.isFull():
			print "error: queue is full"
			return
		else:
			self.tail += 1
			self.queue.append(x)

	def dequeue(self):
		if self.isEmpty():
			print "erroe: queue is empty"
			return
		else:
			self.tail -= 1
			return self.queue.pop(0)

	def show(self):
		print self.queue

if __name__ == '__main__':
	q = Queue(3)
	q.enqueue(1)
	q.enqueue(2)
	q.enqueue(3)
	q.enqueue(4)
	q.show()
	q.dequeue()
	q.show()
```

## 链表

链表中各对象按照线性顺序排列，单向链表的每个元素包括next指针（指向下一个元素）和该元素关键字的值。此外，还有单向循环链表、双向链表和双向循环链表。

```python
class ListNode:
	def __init__(self, x):
		self.val = x
		self.next = None

class SingleLinkedList:
	def __init__(self):
		self._head = None

	def isEmpty(self):
		return self._head == None

	def size(self):
		cur = self._head
		cnt = 0
		while cur:
			cnt += 1
			cur = cur.next
		return cnt
	def add(self, x):
		'''
		insert x before head
		'''
		tmp = ListNode(x)
		tmp.next = self._head
		self._head = tmp

	def append(self, x):
		'''
		append x at the tail
		'''
		tmp = ListNode(x)
		if self.isEmpty():
			self._head = tmp
		else:
			cur = self._head
			while cur.next:
				cur = cur.next
			cur.next = tmp

	def isFound(self, x):
		cur = self._head
		while cur:
			if cur.val == x:
				return True
			cur = cur.next
		return False

	def index(self, x):
		cur = self._head
		cnt = 0
		while cur:
			if cur.val == x:
				return cnt
			cur = cur.next
			cnt += 1
		return -1

	def remove(self, x):
		cur = self._head
		pre = None
		while cur:
			if cur.val == x:
				if not pre:
					self._head = cur.next
				else:
					pre.next = cur.next
				break
			else:
				pre = cur
				cur = cur.next

	def insert(self, pos, x):
		if pos == self.size():
			self.append(x)
		elif pos > self.size():
			print "error: pos is out of index"
		elif pos == 0:
			self.add(x)
		else:
			tmp = ListNode(x)
			cnt = 1
			cur = self._head
			while cnt < pos:
				cur = cur.next
				cnt += 1
			cur.next, tmp.next = tmp, cur.next

	def show(self):
		cur = self._head
		while cur:
			print cur.val,
			cur = cur.next
		print
```

## 指针和对象的实现

- 对象的多数组表示。以双向链表为例，分别使用三个数组存放next, prev, key，对于给定数组下标x，三个数组项next[x], prev[x], key[x]一起表示链表中的一个对象。此外还需要另一个变量存放表头元素的下标。
- 对象的单数组表示。一个对象占用一段连续的子数组nums[j..k]，对象中的每个属性对应于从0到j-k之间的一个偏移量，指向该对象的指针就是下标j。以双向链表为例，对象具有的属性key, next, prev，则可以设置对应的偏移量为0, 1, 2。（在上面的例子中，如果指向某个对象的指针为下标x，则指向下一个对象的指针为下标x+3）

## 对象的分配和释放

略

## 有根树的表示

- 二叉树：属性p, left, right存放指向父节点、左孩子、右孩子的指针。
- 分支无限制的有根树：左孩子右兄弟表示法。x.left-child, x.right-sibling分别指向结点x最左边的孩子结点和x右侧相邻的兄弟结点。