title: 归并排序（Merge sort）
tags: |-

  - 算法
  - sort
  - 排序
permalink: gui-bing-pai-xu-merge-sort
id: 5
updated: '2014-09-23 14:53:22'
date: 2014-07-21 10:49:27
---

**归并算法(merge)**：指的是将两个已经排序的序列合并成一个序列的操作。
![](http://upload.wikimedia.org/wikipedia/commons/c/c5/Merge_sort_animation2.gif)

####归并操作的过程如下：

1. 申请空间，使其大小为两个已经排序序列之和，该空间用来存放合并后的序列
2. 设定两个指针，最初位置分别为两个已经排序序列的起始位置
3. 比较两个指针所指向的元素，选择相对小的元素放入到合并空间，并移动指针到下一位置
4. 重复步骤3直到某一指针到达序列尾
5. 将另一序列剩下的所有元素直接复制到合并序列尾

####java 代码

```java
public int[] MergeSort(int[] A, int[] B) {
	int[] C = new int[A.length + B.length];
	int k = 0;//三个数组的索引
	int i = 0;
	int j = 0;
	while(i < A.length && j < B.length) {
		if (A[i] < B[j])
			C[k++] = A[i++];
		else
			C[k++] = B[j++];
	}
	while (i < A.length) 
		C[k++] = A[i++];
	while (j < B.length) 
		C[k++] = B[j++];
	return C;
}
```