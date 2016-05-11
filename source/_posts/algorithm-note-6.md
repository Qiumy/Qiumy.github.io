layout: post
title: 算法导论笔记（六）—— Chap 9 顺序统计量
comment: true
date: 2016-05-11 10:04:21
tags: [算法,算法导论,技术,笔记] 
---

# 顺序统计量

## 期望为线性时间的选择算法

找出数组nums[p...r]中第i小的元素。最坏运行时间为O(n^2)

<!-- more -->

```python
def partition(nums, p, r):
	x = nums[r]
	i = p-1
	for j in range(p, r):
		if nums[j] <= x:
			i += 1
			nums[i], nums[j] = nums[j], nums[i]
	nums[i+1], nums[r] = nums[r], nums[i+1]
	return i+1

def randomoized_partition(nums, p, r):
	i = random.randint(p,r)
	nums[i], nums[r] = nums[r], nums[i]
	return partition(nums, p, r)

def randomized_select(nums, p, r, i):
	if p==r:
		return nums[p]
	q = randomoized_partition(nums, p, r)
	k = q - p + 1
	if i==k:
		return nums[q]
	elif i<k:
		return randomized_select(nums, p, q-1, i)
	else:
		return randomized_select(nums, q+1, r, i-k)

```
