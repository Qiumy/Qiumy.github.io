layout: post
title: 算法导论笔记（四）—— Chap 7 快速排序
comment: true
date: 2016-05-09 10:19:06
tags: [算法,算法导论,技术,笔记] 
---
# 快速排序

## 描述

对于数组nums[p..r]进行快速排序的三步分治步骤

- 分解：数组被分解成两个（可能为空）子数组nums[p..q-1]和nums[q+1...r]，使得前一个数组的元素都小于等于nums[q]，nums[q]小于等于后一个数组中的每个元素。
- 解决：通过递归调用快速排序，对子数组nums[p..q-1]和nums[q+1...r]进行排序
- 合并：因为子数组都是原址排序的，所以不需要合并操作：数组nums[p..r]已经是有序

<!-- more -->

```python
def quick_sort(nums, p, r):
	if p<r:
		q = partition(nums, p, r)
		quick_sort(nums, p, q-1)
		quick_sort(nums, q+1, r)

def partition(nums, p, r):
	x = nums[r]
	i = p-1
	for j in range(p, r):
		if nums[j] <= x:
			i += 1
			nums[i], nums[j] = nums[j], nums[i]
	nums[i+1], nums[r] = nums[r], nums[i+1]
	return i+1
```

## 扩展

上面的划分总是选择nums[r]作为主元，并围绕它来划分子数组。下面使用随机抽样选取主元。

```python
def randomoized_quicksort(nums, p, r):
	if p<r:
		q = randomoized_partition(nums, p, r)
		randomoized_quicksort(nums, p, q-1)
		randomoized_quicksort(nums, q+1, r)

def randomoized_partition(nums, p, r):
	i = random.randint(p,r)
	nums[i], nums[r] = nums[r], nums[i]
	return partition(nums, p, r)
```

尾递归调用，减少快速排序栈的深度。

```python
def tail_recursive_quicksort(nums, p, r):
	while p<r:
		# partition and sort left subarray
		q = partition(nums, p, r)
		tail_recursive_quicksort(nums, p, q-1)
		p = q+1
```