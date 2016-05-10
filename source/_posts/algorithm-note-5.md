layout: post
title: 算法导论笔记（五）—— Chap 8 线性排序
comment: true
date: 2016-05-10 10:03:32
tags: [算法,算法导论,技术,笔记] 
---

# 线性时间排序

## 计数排序

- 计数排序假设n个输入元素中的每一个都是在0到k内的一个整数，其中k为某个整数。当k=O(n)时，排序的运行时间为O(n)。


- 计数排序的基本思想是：对每一个输入元素x，确定小于x的元素个数。（如果有17个元素小于x，则把x放在第18个输出位置上）。

<!-- more -->

```python
def counting_sort(nums, k):
	'''
	sort nums and the output is res
	the samples of the nums are between 0 to k
	:type nums: list
	:type k: int
	:rtype: list
	'''
	res = [0]*len(nums)
	tmp = [0]*(k+1)
	for i in range(0, len(nums)):
		tmp[nums[i]] = tmp[nums[i]] + 1

	# tmp array now contains the number of elements equal to i
	for i in range(1, k+1):
		tmp[i] = tmp[i] + tmp[i-1]

	# tmp array now contains the number of element less than or equal to i
	for i in range(len(nums)-1, -1, -1):
		res[tmp[nums[i]]-1] = nums[i]
		tmp[nums[i]] = tmp[nums[i]] - 1

	return res
```

## 基数排序

基数排序对于n个d位的元素，先按照最低有效位进行排序。

```python
def radix_sort(nums, d):
	'''
	use a stable sort to sort array nums on digit i
	'''
	res = nums
	for i in range(d-1, -1, -1):
		res = sorted(res, key=lambda num: str(num)[i])
		# print "---", res
	return res
```

## 桶排序

桶排序假设输入数据服从均匀分布，平均情况下时间代价为O(n)。例如输入均匀独立地分布在[0,1)区间上，桶排序将[0,1)区间分成n个相同大小的子区间，将n个输入数分别放在各个桶中。先对每个桶中的数进行排序，然后遍历每个桶，按照次序把各个桶中的元素列出来即可。

```python
def bucket_sort(nums):
	'''
	the samples of the nums are belong [0,1)
	'''
	n = len(nums)
	res = [[] for _ in range(n)]
	ans = []
	for i in range(0, n):
		res[int(nums[i]*10)].append(nums[i])

	for i in range(0, n):
		res[i].sort()
		ans.extend(res[i])
	return ans
```