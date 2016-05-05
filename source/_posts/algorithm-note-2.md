layout: post
title: 算法导论笔记（二）—— Chap 2 分治策略
comment: true
date: 2016-05-05 23:07:54
tags: [算法,技术,笔记] 
---

# 分治策略

## 最大子数组

问题：给定数组A，寻找A的和最大的非空连续子数组

- O(n^2)：A的子数组的个数共有n+n-1+n-2+....+2+1 = n(n+1)/2个。对于每个数组求和，并记录最大值，sum(A[i...j]) = sum(A[i...j-1]) + A[j]

  ```python
  def max_subarray1(nums):
  	max_sum = min(nums)
  	left_idx, right_idx = 0, len(nums)

  	for i in range(len(nums)):
  		for j in range(i, len(nums)):
  			tmp_sum = 0
  			for k in range(i, j+1):
  				tmp_sum += nums[k]
  			if tmp_sum > max_sum:
  				max_sum = tmp_sum
  				left_idx = i
  				right_idx = j
  	return (left_idx, right_idx, max_sum) 
  ```

  < !-- more -->


- 分治算法：A[low,...high]的任何连续子数组A[i,..j]所处的位置必然是一下三种情况之一：

  1. 完全位于子数组A[low,...mid]中，因此low≤i≤j≤mid
  2. 完全位于子数组A[mid+1,...high]中，因此mid<i≤j≤high
  3. 跨越了中点，因此low≤i≤mid<j≤high

  ```python
  def max_subarray2(nums,low,high):
  	if low == high:
  		return (low, high, nums[low])

  	max_sum = min(nums)
  	left_idx, right_idx = 0, len(nums)

  	mid = (low+high)/2
  	cross_low, cross_high = mid, mid

  	left_max_sum = tmp_sum = 0
  	for i in range(mid,low-1,-1):
  		tmp_sum += nums[i]

  		if tmp_sum > left_max_sum:
  			cross_low = i
  			left_max_sum = tmp_sum

  	right_max_sum = tmp_sum = 0
  	for i in range(mid+1, high+1):
  		tmp_sum += nums[i]
  		
  		if tmp_sum > right_max_sum:
  			cross_high = i
  			right_max_sum = tmp_sum

  	left_low, left_high, left_sum = max_subarray2(nums,low,mid)
  	right_low, right_high, right_sum = max_subarray2(nums,mid+1,high)

  	if left_sum >= left_max_sum+right_max_sum and left_sum >= right_sum:
  		return (left_low, left_high, left_sum)
  	elif right_sum >= left_max_sum+right_max_sum and right_sum >= left_sum:
  		return (right_low, right_high, right_sum)
  	else:
  		return (cross_low, cross_high, left_max_sum+right_max_sum)
  ```
