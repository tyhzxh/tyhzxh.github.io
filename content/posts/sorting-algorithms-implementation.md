---
title: "经典排序算法实现与分析"
date: 2024-02-27T22:11:13+08:00
draft: false
tags: ["算法", "排序", "数据结构", "C++", "算法分析"]
categories: ["算法与数据结构"]
description: "详细介绍和实现经典排序算法，包括选择排序、插入排序、冒泡排序、快速排序、归并排序和堆排序，并提供性能测试代码"
cover:
    image: "https://images.unsplash.com/photo-1518186285589-2f7649de83e0?w=800&h=400&fit=crop"
    alt: "排序算法可视化"
---

## 前言

这是某次学校数据结构实验中实现的基本排序算法。数据结构这门课的难点在于实现的复杂性，每次实验都需要封装数据结构，编写测试程序，对于初学者来说确实是一个挑战。

通过这次实验，我深刻体会到了代码质量的重要性。虽然当时的代码可能不够优雅，但这正是学习过程中的宝贵经历。

## 基本数据结构封装

首先定义基本的数据结构和工具函数：

```cpp
#include "stdio.h"
#include <iostream>
#include <ctime>
#include <cstdlib>

#define ERROR 0
#define OK 1
#define Overflow 2
#define Underflow 3
#define NotPresent 4    // 元素不存在
#define Duplicate 5     // 元素重复存在
#define MaxSize 100001

typedef int KeyType;
typedef int Status;
typedef int DataType;

typedef struct Entry {
    KeyType key;
    DataType data;
} entry;

typedef struct list {
    int n;
    Entry D[MaxSize];
} List;

// 输出函数
Status printlist(list* l) {
    for(int i = 0; i < l->n; i++) {
        printf("%d ", l->D[i].key);
    }
    return OK;
}

// 交换函数
void swap(Entry* a, Entry* b) {
    Entry temp = *a;
    *a = *b;
    *b = temp;
}
```

## 排序算法实现

### 1. 简单选择排序

选择排序的基本思想是每次从未排序的元素中选择最小的元素，放到已排序序列的末尾。

```cpp
Status choosesort(list* l) {
    for(int i = 0; i < l->n - 1; i++) {
        int min = i;
        for(int j = i + 1; j < l->n; j++) {
            if(l->D[min].key > l->D[j].key)
                min = j;
        }
        swap(&l->D[i], &l->D[min]);
    }
    return OK;
}
```

**时间复杂度**：O(n²)  
**空间复杂度**：O(1)  
**稳定性**：不稳定

### 2. 直接插入排序

插入排序的基本思想是将一个记录插入到已经排好序的有序表中，从而得到一个新的、记录数增1的有序表。

```cpp
Status insertsort(list* l) {
    int i, j;
    for(i = 1; i < l->n; i++) {
        Entry temp = l->D[i];
        j = i - 1;
        while(j >= 0) {
            if(l->D[j].key > temp.key) {
                l->D[j + 1] = l->D[j];
                l->D[j] = temp;
            }
            j--;
        }
    }
    return OK;
}
```

**时间复杂度**：O(n²)  
**空间复杂度**：O(1)  
**稳定性**：稳定

### 3. 冒泡排序

冒泡排序是一种简单的排序算法，它重复地走访过要排序的数列，一次比较两个元素，如果它们的顺序错误就把它们交换过来。

```cpp
void Bubblesort(list* l) {
    for(int i = 0; i < l->n - 1; i++) {
        int start = 0, end = 1;
        while (end < l->n) {
            if (l->D[start].key > l->D[end].key) {
                Entry temp = l->D[start];
                l->D[start] = l->D[end];
                l->D[end] = temp;
            }
            ++start;
            ++end;
        }
    }
}
```

**时间复杂度**：O(n²)  
**空间复杂度**：O(1)  
**稳定性**：稳定

### 4. 快速排序

快速排序使用分治法策略来把一个序列分为较小和较大的2个子序列，然后递归地排序两个子序列。

```cpp
void QuickSort(List* l, int low, int high) {
    if (low < high) {
        int i = low;
        int j = high;
        Entry pivot = l->D[low]; // 选择第一个元素作为基准
        
        while (i < j) {
            // 从右向左找小于基准的元素
            while (i < j && l->D[j].key > pivot.key) {
                j--;
            }
            if(i < j)
                l->D[i++] = l->D[j];
                
            // 从左向右找大于基准的元素
            while(i < j && l->D[i].key < pivot.key) {
                i++;
            }
            if(i < j)
                l->D[j--] = l->D[i];
        }
        
        l->D[i] = pivot;
        QuickSort(l, low, i - 1);   // 递归排序左半部分
        QuickSort(l, i + 1, high); // 递归排序右半部分
    }
}
```

**时间复杂度**：平均O(n log n)，最坏O(n²)  
**空间复杂度**：O(log n)  
**稳定性**：不稳定

> 快速排序确实很精妙，理解和记忆都需要时间。它的核心思想是分治法，通过选择一个基准元素，将数组分为两部分，然后递归地对两部分进行排序。

### 5. 归并排序

归并排序采用分治法的一个非常典型的应用。将已有序的子序列合并，得到完全有序的序列。

```cpp
void merge(list* l, int left, int lmid, int rmid, int right) {
    int i = left, j = rmid; // i,j指向left和right
    list temp;
    int index = 0;
    
    // 合并两个有序序列
    while(i <= lmid && j <= right) {
        if(l->D[i].key <= l->D[j].key) {
            temp.D[index++] = l->D[i++];
        } else {
            temp.D[index++] = l->D[j++];
        }
    }
    
    // 复制剩余元素
    while(i <= lmid) {
        temp.D[index++] = l->D[i++];
    }
    while (j <= right) {
        temp.D[index++] = l->D[j++];
    }
    
    // 将临时数组的内容复制回原数组
    int n = 0;
    for(int k = left; k <= right; k++) {
        l->D[k] = temp.D[n++];
    }
}

Status mergesort(list* l, int left, int right) {
    if(left < right) {
        int mid = left + (right - left) / 2;
        mergesort(l, left, mid);
        mergesort(l, mid + 1, right);
        merge(l, left, mid, mid + 1, right);
    }
    return OK;
}
```

**时间复杂度**：O(n log n)  
**空间复杂度**：O(n)  
**稳定性**：稳定

### 6. 堆排序

堆排序是指利用堆这种数据结构所设计的一种排序算法。堆是一个近似完全二叉树的结构，并同时满足堆积的性质。

```cpp
void downadjust(list* l, int start, int end) {
    Entry temp = l->D[start];
    for(int i = 2 * start + 1; i <= end; i *= 2) {
        // 如果左子结点小于右子结点
        if(i < end && l->D[i].key > l->D[i + 1].key)
            i++; // i指向右子结点
            
        // 如果父结点大于子结点
        if(temp.key >= l->D[i].key)
            break; // 调整结束
            
        l->D[start] = l->D[i]; // 否则将子结点值赋给父结点
        start = i; // 重新赋值开始指针
    }
    l->D[start] = temp; // 调整结束后，将temp值放在最终位置
}

Status heapsort(list* l) {
    // 建堆
    for(int i = l->n / 2; i >= 0; i--) {
        downadjust(l, i, l->n);
    }
    return OK;
}
```

**时间复杂度**：O(n log n)  
**空间复杂度**：O(1)  
**稳定性**：不稳定

## 测试框架

### 数据初始化

```cpp
list* initlist(int n) {
    list* l;
    l = (list*)malloc(sizeof(list));
    l->n = n;
    
    if(n > MaxSize)
        return nullptr;
        
    srand(time(0)); // 生成随机种子
    for(int i = 0; i < n; i++) {
        l->D[i].key = (rand() % 1000) + 1; // 随机数赋值
        l->D[i].data = (rand() % 50) + 1;
    }
    return l;
}

void deletelist(list* l) {
    if(!l)
        return;
    free(l);
}
```

### 性能测试函数

```cpp
Status testchoosesort(int n) {
    list* l0 = initlist(n);
    printf("\n\nmake a new list!\n");
    printlist(l0);
    
    clock_t start, finish;
    start = clock();
    choosesort(l0);
    finish = clock();
    
    printf("\nafter choosesort\n");
    printlist(l0);
    
    double TheTimes = (double)(finish - start) / CLOCKS_PER_SEC;
    printf("消耗%f秒。\n", TheTimes);
    
    deletelist(l0);
    return OK;
}

Status testquicksort(int n) {
    list* l0 = initlist(n);
    printf("\n\nmake a new list!\n");
    printlist(l0);
    
    clock_t start, finish;
    start = clock();
    QuickSort(l0, 0, l0->n - 1);
    finish = clock();
    
    printf("\nafter quicksort\n");
    printlist(l0);
    
    double TheTimes = (double)(finish - start) / CLOCKS_PER_SEC;
    printf("消耗%f秒。\n", TheTimes);
    
    deletelist(l0);
    return OK;
}

// 主测试函数
int main() {
    printf("测试不同规模数据的排序性能：\n");
    
    testinsertsort(500);
    testchoosesort(500);
    testBubblesort(500);
    testquicksort(500);
    testmergesort(500);
    
    return 0;
}
```

## 算法性能比较

| 排序算法 | 平均时间复杂度 | 最坏时间复杂度 | 最好时间复杂度 | 空间复杂度 | 稳定性 |
|---------|---------------|---------------|---------------|-----------|--------|
| 选择排序 | O(n²) | O(n²) | O(n²) | O(1) | 不稳定 |
| 插入排序 | O(n²) | O(n²) | O(n) | O(1) | 稳定 |
| 冒泡排序 | O(n²) | O(n²) | O(n) | O(1) | 稳定 |
| 快速排序 | O(n log n) | O(n²) | O(n log n) | O(log n) | 不稳定 |
| 归并排序 | O(n log n) | O(n log n) | O(n log n) | O(n) | 稳定 |
| 堆排序 | O(n log n) | O(n log n) | O(n log n) | O(1) | 不稳定 |

## 总结

通过这次排序算法的实现和测试，我们可以得出以下结论：

1. **简单排序算法**（选择、插入、冒泡）实现简单，但时间复杂度较高，适合小规模数据。

2. **高效排序算法**（快速、归并、堆）时间复杂度较低，适合大规模数据处理。

3. **稳定性**在某些应用场景中很重要，需要根据具体需求选择合适的算法。

4. **空间复杂度**也是选择算法时需要考虑的重要因素。

学习排序算法不仅仅是为了应付考试，更重要的是理解算法设计的思想和优化策略。每种算法都有其适用的场景，在实际开发中需要根据具体情况选择最合适的算法。

> 代码质量的提升确实需要大量的练习和思考。虽然当时的代码可能不够完美，但这正是成长过程中的重要一步。