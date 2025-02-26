---
title: Go语言排序算法
date: 2025/01/23
tags: 算法
category: 算法
---


```go
package main

import (
	"fmt"
	"math/rand"
	"sort"
	"time"
)

var r = rand.New(rand.NewSource(time.Now().UnixNano()))

func getArray(size, maxValue int, flag bool) []int {
	arr := make([]int, size)
	nega := 0

	if flag {
		nega = -maxValue
	}

	for i := 0; i < size; i++ {
		arr[i] = r.Intn(maxValue-nega) + nega
	}

	return arr
}

func isEqual(arr1, arr2 []int) bool {
	if len(arr1) != len(arr2) {
		return false
	}
	for i := range arr1 {
		if arr1[i] != arr2[i] {
			return false
		}
	}
	return true
}

func bubbleSort(arr []int) {
	n := len(arr)
	for i := n - 1; i > 0; i-- {
		for j := 0; j < i; j++ {
			if arr[j] > arr[j+1] {
				arr[j], arr[j+1] = arr[j+1], arr[j]
			}
		}
	}
}

func selectSort(arr []int) {
	n := len(arr)
	for i := 0; i < n-1; i++ {
		min := i
		for j := i + 1; j < n; j++ {
			if arr[j] < arr[min] {
				min = j
			}
		}
		arr[i], arr[min] = arr[min], arr[i]
	}
}

func quickSort(arr []int, left, right int) {
	if left >= right {
		return
	}
	i, j := left, right
	p := arr[i]
	for i < j {
		for i < j && arr[j] >= p {
			j--
		}
		arr[i] = arr[j]
		for i < j && arr[i] <= p {
			i++
		}
		arr[j] = arr[i]
	}
	arr[i] = p
	quickSort(arr, left, i-1)
	quickSort(arr, i+1, right)
}

func insertSort(arr []int) {
	n := len(arr)
	for i := 0; i < n-1; i++ {
		for j := i; j >= 0 && arr[j] > arr[j+1]; j-- {
			arr[j], arr[j+1] = arr[j+1], arr[j]
		}
	}
}

func main() {
	size, maxValue, testTime := 100, 100, 100000
	flag := true
	for i := 0; i < testTime; i++ {
		arr := getArray(size, maxValue, false)
		arr1 := make([]int, size)
		copy(arr1, arr)

		insertSort(arr)

		sort.Ints(arr1)
		flag = isEqual(arr, arr1)
		if !flag {
			break
		}
	}
	fmt.Println(flag)
}

```

