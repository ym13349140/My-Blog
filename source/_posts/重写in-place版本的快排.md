---
title: 重写in-place版本的快排
date: 2018-05-11 15:38:05
tags:
    - JS
    - 算法
    - 排序
categories:
    - JS
---

<blockquote class="blockquote-center">之前一直写的快排都是参照阮一峰老师的博客写的，然后发现动态分配太多额外的空间了，然后又重新写了一个in-place版本的快速排序算法。</blockquote>

<!--more-->

### 前言
对于算法这一块，自己本身就不是很熟，所以之前在复习排序算法的时候直接就参考了网上的一些代码，各种排序算法的基本思想是搞懂了，但是并没有想过去很好的优化一下。比如说之前写的快排，就是直接参考了阮一峰老师的博客里的快排，因为当时觉得这个写法最简单，看起来最清晰。但是今天跟人在讨论的时候发现其实那种方法并不是很好，因为每次递归的时候都开辟了两个新的辅助数组，这样一层层下来其实耗费了很多额外的空间，尤其是在数组很大的时候；还有一个问题就是阮老师的方法对原数组的长度也产生了改变，splice操作不仅增加了O(n)的复杂度，还减少了原数组的一个元素。
最好的快排还是应该还是在原数组的基础上进行元素交换来实现，不需要占用太多的辅助空间。然后自己又参考了网上的一些基于原数组的快排，写出了下面这两种in-place版本的快排，第一种是基于while循环，第二种是基于for循环实现。

### 基于while循环的快排（quickSort）
```JavaScript
function swap(arr, left, right) {
  [arr[left], arr[right]] = [arr[right], arr[left]];
}

// partition 函数分开版
function partition(arr, left, right) {
    let mid = Math.floor(left + ((right - left) >> 1)),
        base = arr[mid],
        start = left,
        end = right;
    while (start < end) {
        while (arr[start] <= base) {
            start++;
        }
        while (arr[end] > base) {
            end--;
        }
        if (start < end) {
            if (start == mid) {
                mid = end;
            } else if (end == mid) {
                mid = start;
            }
            swap(arr, start, end);
            start++;
            end--;
        }
    }
    // 这里的判断是关键,最后需要将base元素交换回返回的位置
    if ((start == end) && (arr[end] > base)) {
        if (mid != end - 1) {
            swap(arr, mid, end - 1);
        }
        return end - 1;
    } else {
        if (mid != end) {
            swap(arr, mid, end);
        }
        return end;
    }
}

function mysort(arr, left, right) {
  if (left >= right) {
    return;
  }
  let mid = partition(arr, left, right);
  mysort(arr, left, mid - 1);
  mysort(arr, mid + 1, right);
}

function quickSort(arr) {
  let left = 0,
    right = arr.length - 1;
  mysort(arr, left, right);
}

// 单个函数综合版
function myQuickSort(arr, left, right) {
    left = left == undefined ? 0 : left;
    right = right == undefined ? arr.length - 1 : right;
    let baseIndex = Math.floor(left + ((right - left) >> 1)),
        base = arr[baseIndex],
        start = left,
        end = right;
    if (left >= right) {
        return;
    }
    while (start < end) {
        while (arr[start] <= base) {
            start++;
        }
        while (arr[end] > base) {
            end--;
        }
        if (start < end) {
            if (start == baseIndex) {
                baseIndex = end;
            } else if (end == baseIndex) {
                baseIndex = start;
            }
            swap(arr, start, end);
            start++;
            end--;
        }
    }
    // 这里的判断是关键
    if ((start == end) && (arr[end] > base)) {
        if (baseIndex != end - 1) {
            swap(arr, baseIndex, end - 1);
        }
        baseIndex = end - 1;
    } else {
        if (baseIndex != end) {
            swap(arr, baseIndex, end);
        }
        baseIndex = end;
    }
    myQuickSort(arr, left, baseIndex - 1);
    myQuickSort(arr, baseIndex + 1, right);
}
```
这上面值得注意的就是注释部分的那个判断条件，这是必不可少的，因为当跳出while循环的时候这里存在以下两种情况：
> 1. 最后剩两个元素，即 start > end，且start=end+1，此时base元素的位置一定位于end处，因为此位置左边的数全部都小于等于 base，所以需要将base元素交换到end位置，然后直接返回end。
> 2. 最后剩一个元素，start = end，此时base元素有两种可能：arr[end] <= base 和 arr[end] > base，对于前者，其实与情况一相同，将base元素交换到end位置，然后返回end；对于后者，当 arr[end] > base 时，base元素的位置应该位于 end-1 的位置，所以交换base元素与end-1位置的元素，然后返回end-1 即可。

这两种情况的示意图如下：
{% qnimg JS/quickSort-if-v2.jpg title:quickSort-if alt:快排判断条件示例图 extend:?imageView2/2/w/550 %}

### 基于for循环的快排（quickSort）
```JavaScript
function quickSort(arr) {
  function swap(arr, left, right) {
    [arr[left], arr[right]] = [arr[right], arr[left]];
  }

  function partition(arr, left, right) {
    let midIndex = left; // midIndex用于存储base元素的位置
    let base = arr[right];
    for (let i = left; i < right; i++) {
      if (arr[i] < base) {
        swap(arr, i, midIndex);
        midIndex++; // 每次交换之后将midIndex加一
      }
    }
    swap(arr, midIndex, right); //最后还需要将事先放在最末尾的base值交换回midIndex位置来
    return midIndex;
  }

  function mysort(arr, left, right) {
    let mid = partition(arr, left, right);
    if (mid >= left + 1) {
      mysort(arr, left, mid - 1);
    }
    if (mid + 1 <= right) {
      mysort(arr, mid + 1, right);
    }
  }
  mysort(arr, 0, arr.length - 1);
}
```

### 总结
关于for循环和while循环的对比：
> 1. 从稳定性上来说，for循环的稳定性会比while循环更高，因为for循环是单向循环，交换过程不影响既定的元素先后顺序，而while循环是双向循环，前后多次交换的时候可能会影响原来的元素先后顺序，所以是不稳定的。
> 2. 从代码量来说，下面的for循环可能更简短一些。
> 3. 从结构清晰度来说，while循环的分治形式对于快排的分治思想体现的会更加直白一点，会让人更容易看懂一点。