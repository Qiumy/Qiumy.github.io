layout: post
title: 算法导论笔记（三）—— Chap 6 堆排序
comment: true
date: 2016-05-08 09:56:44
tags: [算法,算法导论,技术,笔记] 
---

# 堆排序

## 堆

二叉堆是一个数组，近似的完全二叉树，给定结点的下标i，容易获得其父节点、左孩子、有孩子的下标。

```python
def parent(i):
	return (i-1)/2
def left(i):
	return 2*i+1
def right(i):
	return 2*(i+1)
```

<!-- more -->

## 维护堆的性质

最大堆：父结点的值大于左右子树根节点的值

最小堆：父结点的值小于左右子树根节点的值

```python
def max_heapify(nums, pos, heapsize):
	'''
	contain a heap from node pos
	'''
	l = left(pos)
	r = right(pos)
	largest = pos
	if l < heapsize and nums[l] > nums[pos]:
		largest = l
	if r < heapsize and nums[r] > nums[largest]:
		largest = r
	if largest != pos:
		nums[pos], nums[largest] = nums[largest], nums[pos]
		max_heapify(nums, largest, heapsize)
```

## 建立堆

从结点`(len(nums)-1)/2`到结点`0`对最大堆进行维护

```python
def build_max_heap(nums):
	'''
	bulid a max heap
	'''
	for i in range((len(nums)-1)/2,-1,-1):
		max_heapify(nums, i, len(nums))
```

## 堆排序算法

最大的元素总是在`nums[0]`，通过交换结点`0`和`len(nums)-1`的值，从堆中去掉结点`len(nums)-1`，剩余的结点依旧维持最大堆得性质，重复上述步骤。

```python
def heap_sort(nums):
	'''
	use a heap to sort
	'''
	build_max_heap(nums)
	heapsize = len(nums)
	for i in range(len(nums)-1,0,-1):
		nums[0], nums[i] = nums[i], nums[0]
		heapsize -= 1
		max_heapify(nums,0,heapsize)
```

## 优先队列

最大优先队列会支持一下操作：插入元素、返回最大关键字的元素、去掉并返回最大关键字的元素、将某个元素的关键字值增加到k。

下面使用堆进行实现：

```python
def insert(nums, x):
	nums.append(-2172738173)
	increase_key(nums, len(nums)-1, x)

def maxinum(nums):
	return nums[0]

def extract_max(nums):
	if len(nums) < 0:
		print "error, heap underflow"
		return
	max_num = nums[0]
	nums[0] = nums[len(nums)-1]
	nums.pop()
	max_heapify(nums,0, len(nums))
	return max_num

def increase_key(nums, i, key):
	if key<nums[i]:
		print "error, new key is smaller than current key"
	nums[i] = key
	while i>0 and nums[parent(i)]<nums[i]:
		nums[i], nums[parent(i)] = nums[parent(i)], nums[i]
		i = parent(i)
```