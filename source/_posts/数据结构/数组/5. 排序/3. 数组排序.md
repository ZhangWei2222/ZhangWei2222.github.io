---
title: 手撕快速排序
date: 2021-07-31 10:28:48
categories:
- 数据结构
comments: true
---

[**题目描述：**](https://leetcode-cn.com/problems/sort-an-array/)给你一个整数数组 nums，请你将该数组升序排列。

<!-- more -->

> 示例 1：
> 输入：nums = [5,2,3,1]
> 输出：[1,2,3,5]
>
> 示例 2：
> 输入：nums = [5,1,1,2,0,0]
> 输出：[0,0,1,1,2,5]



## [方法一：快排](https://leetcode-cn.com/problems/sort-an-array/solution/dong-hua-mo-ni-yi-ge-kuai-su-pai-xu-wo-x-7n7g/)

不稳定排序

1. 先从数组中找一个基准数

2. 让其他比它大的元素移动到数列一边，比他小的元素移动到数列另一边，从而把数组拆解成两个部分。

3. 再对左右区间重复第二步，直到各区间只有一个数。

![快速排序](https://pic.leetcode-cn.com/1615459346-rdxcpB-file_1615459346758)

```java
import java.util.Random;

public class Solution {
  public int[] sortArray(int[] nums) {
    quickSort(nums, 0, nums.length-1);
    return nums;
  }

  public void quickSort (int[] nums, int left, int right) {
    if (left < right) {
      int index = partition(nums, left, right);
      quickSort(nums, left, index - 1);
      quickSort(nums, index + 1, right);
    } 
  }

  /**
     * 在数组 nums 的子区间 [left, right] 执行 partition 操作，返回 nums[left] 排序以后应该在的位置
     * 在遍历过程中保持循环不变量的语义
     * 1、[left + 1, i] < nums[left]
     * 2、(i, j] >= nums[left]
     *
     * @param nums
     * @param left
     * @param right
     * @return
     */
  private int partition(int[] nums, int left, int right) {
    // 在区间随机选择一个元素作为标定点
    if (right > left) {
      swap(nums, left, (int) (Math.random() * (right - left) + left));
    }

    int pivot = nums[left];
    int i = left;
    for (int j = left + 1; j <= right; j++) {
      if (nums[j] < pivot) {
        // 小于 pivot 的元素都被交换到前面
        i++;
        swap(nums, i, j);
      }
    }
    // 在之前遍历的过程中，满足 [left + 1, i] < pivot，并且 (i, j] >= pivot
    swap(nums, left, i);
    // 交换以后 [left, i - 1] < pivot, nums[i] = pivot, [i + 1, right] >= pivot
    return i;
  }

  private void swap(int[] nums, int i, int j) {
    if(i != j) {
      int temp = nums[i];
      nums[i] = nums[j];
      nums[j] = temp;
    }
  }
} 
```

**复杂度分析：**

- 时间复杂度：O(nlogn)，这里 N 是数组的长度。第一次交换，算法复杂度为O(N)，然后继续处理两边的数据，再合并，合并操作的算法复杂度是O(1)，于是总的算法复杂度是O(N*logN)（可以这么理解，每次交换用了N，一共logN次）

- 空间复杂度：O(h)，其中 h 为递归调用的层数。最坏情况下需 O(n) 的空间，最优情况下每次都平衡，此时整个递归树高度为  log*n*，空间复杂度为 O(log*n*)。

**执行结果：**通过

- 执行用时：11 ms, 在所有 Java 提交中击败了85.37%的用户

- 内存消耗：47.4 MB, 在所有 Java 提交中击败了63.49%的用户



## 方法二：堆排序

不稳定排序

- 将待排序序列构造成一个大顶堆
  - 注意：这里使用的是数组，而不是一颗二叉树

- 此时：整个序列的 **最大值就是堆顶的根节点**

- 将其 **与末尾元素进行交换**，此时末尾就是最大值

- 然后将剩余 n-1 个元素重新构造成一个堆，这样 就会得到 n 个元素的次小值。如此反复，便能的得到一个有序序列。

```java
class Solution {
  public int[] sortArray(int[] nums) {
    int heapSize = nums.length;
    buildMapHeap(nums, heapSize); // 构建好了一个大顶堆
    // 进行下沉 大顶堆是最大元素下沉到末尾
    for(int i = nums.length - 1; i >=  1; i--) {
      swap(nums, 0, i);
      --heapSize; // 下沉后的元素不参与到大顶堆的调整
      maxHeapify(nums, 0, heapSize); // 重新调整大顶堆
    }
    return nums;
  }

  // 自下而上构建一颗大顶堆
  public void buildMapHeap(int[] nums, int heapSize) {
    for(int i = (int)(Math.floor(heapSize / 2) - 1); i >= 0; i--) {
      maxHeapify(nums, i, heapSize);
    }
  }

  // 从左向右，自上而下的调整节点
  public void maxHeapify(int[] nums, int i, int heapSize) {
    int l = i * 2 + 1, r = i * 2 + 2, largest = i;
    if(l < heapSize && nums[l] > nums[largest]) {
      largest = l;
    }
    if(r < heapSize && nums[r] > nums[largest]) {
      largest = r;
    }
    if(largest != i) {
      swap(nums, i, largest); // 进行节点调整
      // 继续调整下面的非叶子节点
      maxHeapify(nums, largest, heapSize);
    }
  }

  public void swap(int[] nums, int i, int j) {
    int temp = nums[i];
    nums[i] = nums[j];
    nums[j] = temp;
  }
}
```

**复杂度分析**

- 时间复杂度：O(nlogn)。初始化建堆的时间复杂度为 O(n)，建完堆以后需要进行 n−1 次调整，一次调整（即 maxHeapify） 的时间复杂度为 O(logn)，那么 n−1 次调整即需要 O(nlogn) 的时间复杂度。因此，总时间复杂度为 O(n+nlogn)=O(nlogn)。

- 空间复杂度：*O*(1)。只需要常数的空间存放若干变量。

**执行结果：**通过

- 执行用时：11 ms, 在所有 Java 提交中击败了85.37%的用户

- 内存消耗：47.4 MB, 在所有 Java 提交中击败了63.49%的用户