---
title: Go语言链表
date: 2025/01/23
tags: 算法
category: 算法
---

```go
package main

import "fmt"

type ListNode struct {
	Val  int
	Next *ListNode
}

var list *ListNode

func createList(arr []int) {
	head := &ListNode{}
	cur := head
	for _, v := range arr {
		cur.Next = &ListNode{Val: v}
		cur = cur.Next
	}
	list = head.Next
}

func printList() {
	head := list
	for head != nil {
		fmt.Printf("%d ", head.Val)
		head = head.Next
	}
	fmt.Println()
}

func insertListIndex(index, val int) {
	head := list
	for i := 0; i < index-1; i++ {
		head = head.Next
	}
	head.Next = &ListNode{Val: val, Next: head.Next}
}

func deleteListIndex(index int) {
	head := list
	for i := 0; i < index-1; i++ {
		head = head.Next
	}
	head.Next = head.Next.Next
}

func insertListLast(val int) {
	head := list
	for head.Next != nil {
		head = head.Next
	}
	head.Next = &ListNode{Val: val}
}

func insertListHead(val int) {
	head := list
	head = &ListNode{Val: val, Next: head}
	list = head
}

func updateListIndex(index, val int) {
	head := list
	for i := 0; i < index; i++ {
		head = head.Next
	}
	head.Val = val
}

func main() {
	arr := []int{1, 2, 3, 4, 5, 7, 6}
	createList(arr)
	printList()
	insertListIndex(4, 10)
	printList()
	deleteListIndex(4)
	printList()
	insertListLast(100)
	printList()
	insertListHead(1000)
	printList()
	updateListIndex(4, 10000)
	printList()
}

```

