layout: post
title: 算法导论笔记（一）—— Chap 2 算法基础
comment: true
date: 2016-05-03 20:03:04
tags: [算法,技术,笔记] 
---

# 第2章 算法基础

## 插入排序

```python
# 升序排列
import random
def insertion_sort1(nums):
	'''
	:type nums: List
	:rtype: List
	'''
	for j in range(1,len(nums)):
		key = nums[j]
		i = j-1
		while i>=0 and nums[i] > key:
			nums[i+1] = nums[i]
			i = i - 1
		nums[i+1] = key
	return nums

# 降序排列
def insertion_sort2(nums):
	'''
	:type nums: List
	:rtype: List
	'''
	for j in range(1,len(nums)):
		key = nums[j]
		i = j-1
		while i>=0 and nums[i] < key:
			nums[i+1] = nums[i]
			i = i - 1
		nums[i+1] = key
	return nums

if __name__ == '__main__':
	nums = [random.randint(0,99) for _ in range(10)]
	print nums
	print insertion_sort1(nums)
	print insertion_sort2(nums)
```

<!-- more -->

## 归并排序

```python
import random

def merge_sort(nums):
	'''
	:type nums: List
	:rtype : List
	'''
	if len(nums) <= 1:
		return nums
	mid = len(nums)/2
	left = merge_sort(nums[:mid])
	right = merge_sort(nums[mid:])

	return merge(left,right)

def merge(left, right):
	result = []
	i, j = 0, 0
	while i<len(left) and j<len(right):
		if left[i] <= right[j]:
			result.append(left[i])
			i += 1
		else:
			result.append(right[j])
			j += 1
	result += left[i:]
	result += right[j:]
	return result

if __name__ == '__main__':
	nums = [random.randint(0,99) for _ in range(10)]
	print nums
	print merge_sort(nums)
```

## 归并排序变形——求逆序对的数量

逆序对满足：若i < j且A[i] > A[j]

修改`merge`函数即可,但下面的函数并没有对数组排序

```python
import random

def count_inverse(nums):
	'''
	:type nums: List
	:rtype : List
	'''
	if len(nums) <= 1:
		return 0
	mid = len(nums)/2

	left = nums[:mid]
	right  = nums[mid:]
	merge(left,right)
	cnt1 = count_inverse(left)
	cnt2 = count_inverse(right)

	return cnt1 + cnt2 + merge(left,right)

def merge(left, right):
	i, j = 0, 0
	count = 0
	result = []
	len_l = len(left)
	len_r = len(right)
	while i<len(left) and j<len(right):
		if left[i] <= right[j]:
			result.append(left[i])
			i += 1
		else:
			result.append(right[j])
			j += 1
			count += len_l - i
	result += left[i:]
	result += right[j:]
	return count

if __name__ == '__main__':
	# nums = [random.randint(0,99) for _ in range(5)]
	nums = [8, 2, 3, 6, 1, 9, 5] 
	print nums
	print count_inverse(nums)
	print nums
```

对上面的代码进行修改后，同时可以对数组排序

```python
def merge(left, right, nums, n): 
    '''
    :type nums: List
    :rtype : int
    ''' 
    cnt = cnt_l = cnt_r =0  
    count = 0 
    len_l = len(left)
    len_r = len(right)
    while cnt < n:  
        if cnt_l == len_l:  
            while cnt < n:  
                nums[cnt] = right[cnt_r]  
                cnt = cnt + 1  
                cnt_r = cnt_r + 1  
        elif cnt_r == len_r:  
            while cnt < n:  
                nums[cnt] = left[cnt_l]  
                cnt = cnt + 1  
                cnt_l = cnt_l + 1  
        else:  
            if left[cnt_l] > right[cnt_r]:  
                nums[cnt] = right[cnt_r]  
                count = count + len_l - cnt_l  
                cnt = cnt + 1  
                cnt_r = cnt_r + 1  
            else:  
                nums[cnt] = left[cnt_l]  
                cnt = cnt + 1  
                cnt_l = cnt_l + 1  
    return count  
  
def count_inverse(nums):  
    n = len(nums)  
    if n == 1: return 0  
    n1 = n/2  
    n2 = n - n1  
    left = nums[:n1]  
    right = nums[n1:]  
    count1 = count_inverse(left)  
    count2 = count_inverse(right)  
    count = count1 + count2 + merge(left, right, nums, n)  
    return count 
```

