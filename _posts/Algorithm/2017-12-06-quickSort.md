---
layout: post
title: 快速排序法
category: 算法
---

## 简介

1960年，托尼·霍尔首次提出了这种算法，顾名思义，它是一种非常快速的排序算法，时间复杂度和归并排序一样是O(NlogN)，空间上也只需要很小的辅助栈，这使得它的应用十分广泛。

## 原型

+ 首先我们选取数组左端的第一个元素，我们称之为**基准元素**。
+ 然后我们假设可以对**基准元素**后面的数组进行**处理**并找到一个**分割元素**，**分割元素**左边的部分都小于**基准元素**，右边的部分都大于**基准元素**。
+ 接下来将**基准元素**与**分割元素**交换，如此整个数组便被**基准元素**分为了小于它和大于它的两个部分。
+ 运用递归，重复上面的步骤对左右两边的部分排序。

## 处理数组并确定分割点

接下来我们详细讲讲上述思想的第二步。

+ 首先我们要确定**分割元素**必须要是小于**基准元素**的，大家可以先看完后面的部分再来体会这一点。
+ 我们先将分割点放在**基准元素**上，按兵不动，并遍历后面的数组。
+ 一旦我们找到了比**基准元素**小的元素，此时这个元素与**基准元素**之间的部分都是大于**基准元素**的，因此我们将这个元素与**分割元素**（此时就是**基准元素**）后面的第一个元素进行交换（如果基准元素后的第一个元素就比它小，那么交换就在原地发生，实际上也就是没有发生），而分割点的位置也要往后移动一次。
+ 遍历结束后，分割点指向比**基准元素**小的部分的最后一个元素，这个元素就是**分割元素**，而它后面的部分都大于**基准元素**，我们将**分割元素**
与**基准元素**交换后则使包括第一个元素在内的整个数组被**分割元素**分为了两部分。
  
  > 文字描述可能不太容易明白，我们来编一个数组看一看这个过程。
   **6** 9 4 1 8
   其中6是基准元素，分割点也指向它。
   开始遍历它后面的部分
   9比6大，什么也不做，继续下一个。
   4比6小，将它与分割点后的第一个元素交换，也就是和9交换。
   **6** 4 9 1 8
   分割点向后移动一次，指向4。
   继续遍历，1比6小，同理，和9交换。
   **6** 4 1 9 8
   分割点向后移动，指向1。
   继续遍历，8比6大，什么也不做，遍历结束，此时分割元素确定为1，将1和6交换
   1 4 6 9 8
   可以看到此时6左边的部分比6小，右边的部分比6大。

## 实现

```
int __partition(int arr[], int l, int r){

    //将数组的第一个元素设为基准元素，用一个变量保存
    int base = arr[l];
    //同时将第一个位置设为分割点，也保存下来
    int partition = l;
    //从第二个元素开始遍历，直至末尾
    for (int i = l + 1; i <= r; i ++) {
        //如果当前元素小于基准元素，则将它与分割点后的第一个元素交换，分割点后移一次
        if(arr[i] < base){
            swap(arr[partition + 1], arr[i]);
            partition ++;
        }
    }
    //遍历结束后交换基准元素与分割元素，返回分割点
    swap(arr[l], arr[partition]);
    return partition;
}

void __quickSort(int arr[], int l, int r){

    //当子数组只有一个元素时返回
    if(l >= r){
        return;
    }

    //获取分割点，此时左边的部分比分割元素小，右边的部分比分割元素大
    int partition = __partition(arr, l, r);
    //对左边的部分递归处理
    __quickSort(arr, l, partition - 1);
    //对右边的部分递归处理
    __quickSort(arr, partition + 1, r);


}

//同归并排序，暴露给外界调用的方法
void quickSort(int arr[], int n){

    __quickSort(arr, 0, n - 1);

}

```

## 测试

### 随机数组排序

```
selection sort : 2.812375s
insertion sort2 : 1.800698s
merge sort : 0.008420s
quick sort : 0.006709s
```

对50000个数据排序，快速排序拔得头筹。

### 近乎有序的数组排序

```
selection sort : 2.818736s
insertion sort2 : 0.001109s
merge sort : 0.001831s
quick sort : 0.360822s
```

而我们将条件换为近乎有序的数组后，发现快排的速度大大降低。

### 完全有序的数组排序

```
selection sort : 2.790199s
insertion sort2 : 0.000236s
merge sort : 0.000309s
quick sort : 2.774626s
```

数组完全有序时，快排的耗时与选择排序相当，我们猜测此时它已经退化成了O(n²)的排序。

## 优化

首先来分析一下原因。

我们回想一下归并排序为什么是一个O(NlogN)的排序，

这是因为归并排序采用了严格的二分，对于一个长度16的数组，我们可以确定只需要递归4次（即log16为4），即可完成排序。

而对于快速排序，它的分割点并一定在数组的正中央，而这使它的递归次数并不稳定。

我们来看看数组完全有序的极端情况。

> 1 2 3 4

显然此时1就是分割元素，1的左边没有数组，递归处理右边的部分。

> 2 3 4

同理2也是分割元素，和1一样，继续递归处理右边部分。

> 3 4

3是分割元素，处理右边的部分。

> 4 

数组只剩一个元素，返回。

此时，我们递归了4次，算法的复杂度是O(4*4)即O(N²)级别。

我们知道最理想的情况是每次递归时，左边的部分与右边的部分在规模上是平衡的。

如果数组趋于有序，那么左右两边的平衡性将会变得非常差。

为了解决这一点，我们不能在像原来那样选择数组的第一个元素为基准元素，而是要随机挑选基准点。

其实很简单，在确定基准元素前，随机选取一个元素，与数组的第一个元素交换，就完成了优化。

## 实现

只加了一行代码

```
int __partition(int arr[], int l, int r){

    //随机选取一个元素与第一个元素交换
    swap(arr[l], arr[rand() % (r-l+1) + l]);
    //将数组的第一个元素设为基准元素，用一个变量保存
    int base = arr[l];
    //同时将第一个位置设为分割点，也保存下来
    int partition = l;
    //从第二个元素开始遍历，直至末尾
    for (int i = l + 1; i <= r; i ++) {
        //如果当前元素小于基准元素，则将它与分割点后的第一个元素交换，分割点后移一次
        if(arr[i] < base){
            swap(arr[partition + 1], arr[i]);
            partition ++;
        }
    }
    //遍历结束后交换基准元素与分割元素，返回分割点
    swap(arr[l], arr[partition]);
    return partition;
}

```

## 测试

### 近乎有序和完全有序的数组排序

```
selection sort : 2.809105s
insertion sort2 : 0.001413s
merge sort : 0.001978s
quick sort : 0.005191s
```

```
selection sort : 2.771836s
insertion sort2 : 0.000220s
merge sort : 0.000270s
quick sort : 0.005497s
```

尽管此时快排的速度不是最好的，但已经有了很大的提升。

这时我们来追加一个测试，对50000个0到10的随机数进行排序。

### 大量重复元素的数组排序

```
selection sort : 2.824895s
insertion sort2 : 1.706098s
merge sort : 0.006313s
quick sort : 0.260733s
```

这个速度是不是有点眼熟，我们让情况再极端一点。

### 完全重复元素的数组排序

```
selection sort : 2.832416s
insertion sort2 : 0.000242s
merge sort : 0.000288s
quick sort : 2.820966s
```

不出意外地，快速排序再一次退化为了O(N²)的排序，我们在下一篇文中来介绍一下如何针对它来优化这种情况。
