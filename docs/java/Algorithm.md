[Home](../../README)
[TOC]

# Java

## Algorithm

#### 数组排序
排序问题是最基础最简单的算法。
排序方法 | 时间复杂度（平均） | 时间复杂度（最坏） | 时间复杂度（最优） | 空间复杂度 | 稳定性<br>*若 a 在 b 前且 a=b，排序后 a 仍在 b 前则稳定，反之则不稳定。*
-- | -- | -- | -- | -- | --
选择排序 | *O*(n<sup>2</sup>) | *O*(n<sup>2</sup>) | *O*(n<sup>2</sup>) | *O*(1) | 不稳定
插入排序 | *O*(n<sup>2</sup>) | *O*(n<sup>2</sup>) | *O*(n) | *O*(1) | 稳定
冒泡排序 | *O*(n<sup>2</sup>) | *O*(n<sup>2</sup>) | *O*(n) | *O*(1) | 稳定
希尔排序 | *O*(n<sup>1.3</sup>) | *O*(n<sup>2</sup>) | *O*(n) | *O*(1) | 不稳定
快速排序 | *O*(n\*log<sub>2</sub>n) | *O*(n<sup>2</sup>) | *O*(n\*log<sub>2</sub>n) | *O*(1) | 不稳定
归并排序 | *O*(n\*log<sub>2</sub>n) | *O*(n\*log<sub>2</sub>n) | *O*(n\*log<sub>2</sub>n) | *O*(n) | 稳定
堆排序 | *O*(n\*log<sub>2</sub>n) | *O*(n\*log<sub>2</sub>n) | *O*(n\*log<sub>2</sub>n) | *O*(1) | 不稳定
计数排序 | *O*(n+k) | *O*(n+k) | *O*(n+k) | *O*(n+k) | 稳定
桶排序 | *O*(n+k) | *O*(n<sup>2</sup> | *O*(n) | *O*(n+k) | 稳定
基数排序 | *O*(n\*k) | *O*(n\*k) | *O*(n\*k) | *O*(n+k) | 稳定
具体思想与实现：
- **选择排序**
在未排序序列中找到最小元素，存放到排序序列的起始位置，然后再从剩余未排序元素中继续寻找最小元素，然后放到已排序序列的末尾。以此类推，直到所有元素均排序完毕。
    1. 遍历无序区，找到最小元素，与队首元素交换。
    2. 交换后的队首元素为有序区，无序区的大小减少，有序区的大小增大。
    3. 重复步骤 1~2，直至排序完成。
    ```kotlin
    fun selectionSort(array: IntArray): IntArray {
        val length = array.size
        var minIndex: Int
        for (i in 0 until length) {
            minIndex = i
            for (j in i + 1 until length) {
                if (array[j] < array[minIndex]) {
                    minIndex = j
                }
            }
            array[i] = array[minIndex].also { array[minIndex] = array[i] }
        }
        return array
    }
    ```

- **插入排序**
通过构建有序序列，对于未排序数据，在已排序序列中从后向前扫描，找到相应位置并插入。
    1. 从第一个元素开始，认为该元素已排序。
    2. 取出下一个元素，在已经排序的元素序列中从后向前扫描，并找到合适的位置插入。
    3. 针对已排序后的所有元素重复 1~2 步骤。
    ```kotlin
    fun insertSort(array: IntArray, gap: Int = 1): IntArray {
        val length = array.size
        var preIndex: Int
        for (i in 0 until gap) {
            for (j in i until length step gap) {
                preIndex = j - gap
                val currentItem = array[j]
                while (preIndex >= 0 && array[preIndex] > currentItem) {
                    array[preIndex + gap] = array[preIndex]
                    preIndex -= gap
                }
                array[preIndex + gap] = currentItem
            }
        }
        return array
    }
    ```

- **冒泡排序**
重复地走访过要排序的数列，每次比较两个元素，如果它们的顺序错误就把它们交换过来。走访数列的工作是重复地进行直到没有再需要交换。
    1. 比较相邻的元素。如果前者比后者大，就交换它们两个。
    2. 对每一对相邻元素作同样的工作，从开始第一对到结尾的最后一对，这样在最后的元素应该会是最大的数。
    3. 针对所有的元素重复以上的步骤，除了最后一个。
    4. 重复步骤 1~3，直至排序完成。
    ```kotlin
    fun bubbleSort(array: IntArray): IntArray {
        val length = array.size
        for (i in 0 until length) {
            for (j in 0 until length - i) {
                if (array[j] > array[j + 1]) {
                    array[j] = array[j + 1].also { array[j + 1] = array[j] }
                }
            }
        }
        return array
    }
    ```
- **希尔排序**
插入排序的改进版。它与插入排序的不同之处在于，它会优先比较距离较远的元素。希尔排序又叫缩小增量排序。
    1. 选择一个增量序列 t<sub>1</sub>，t<sub>2</sub>，…，t<sub>k</sub>，其中 i<j 时 t<sub>i</sub>>t<sub>j</sub> 且 t<sub>k</sub>=1。
    2. 按增量序列个数 k，对序列进行 k 轮排序。
    3. 每轮排序，根据对应的增量 t<sub>i</sub>，将待排序列分割成若干长度为 m 的子序列，分别对各子表进行直接插入排序。
    4. 每轮排序后更新 t 值，k 轮排序后整个数列排序完成。
    ```kotlin
    fun shellSort(array: IntArray): IntArray {
        val length = array.size
        var gap = 1
        while (gap < length / 3) {
            gap = gap * 3 + 1
        }
        while (gap > 0) {
            insertSort(array, gap)
            gap /= 3
        }
        return array
    }
    ```

- **快速排序**
每次排序将待排记录分隔成独立的两部分，其中一部分记录的关键字均比另一部分的关键字小，对这两部分记录继续进行排序，以达到整个序列有序。
    1. 从数列中挑出一个元素，作为基准（pivot）。
    2. 重新排序数列，所有元素比基准值小的摆放在前面，所有元素比基准值大的摆在后面，与基准相等的数可以到任一边。
    3. 继续把子数列进行快速排序。
    4. 递归步骤 1~3 直至排序完成。
    ```kotlin
    fun quickSort(array: IntArray, left: Int = 0, right: Int = array.size - 1, level: Int = 0): IntArray {
        if (right - left <= 1) {
            return array
        }
        val pivotIndex = (left + right) / 2
        array[right] = array[pivotIndex].also { array[pivotIndex] = array[right] }
        val pivot = array[right]
        var i = left
        var j = right - 1
        while (i <= j) {
            when {
                array[i] <= pivot -> i++
                array[j] >= pivot -> j--
                else -> array[i] = array[j].also { array[j] = array[i] }
            }
        }
        array[right] = array[i].also { array[i] = array[right] }
        if (left < i - 1) {
            quickSort(array, left, i - 1, level + 1)
        }
        if (right > j + 1) {
            quickSort(array, j + 1, right, level + 1)
        }
        return array
    }
    ```

- **归并排序**
归并排序是建立在归并操作上的一种有效的排序算法。先使每个子序列有序，再使子序列段间有序，将已有序的子序列合并，得到完全有序的序列。
    1. 把长度为 n 的输入序列分成两个长度为 n/2 的子序列。
    2. 对这两个子序列分别采用归并排序。
    3. 将两个排序好的子序列合并成一个最终的排序序列。
    4. 递归步骤 1~3 直至排序完成。
    ```kotlin
    fun mergeSort(array: IntArray): IntArray {
        val length = array.size
        if (length < 2) return array
        val middle = length / 2
        val left = array.sliceArray(0 until middle)
        val right = array.sliceArray(middle until length)
        return merge(mergeSort(left), mergeSort(right))
    }

    fun merge(left: IntArray, right: IntArray): IntArray {
        val leftLength = left.size
        val rightLength = right.size
        val result = IntArray(leftLength + rightLength)
        var leftIndex = 0
        var rightIndex = 0
        var resultIndex = 0
        while (leftIndex < leftLength && rightIndex < rightLength) {
            if (left[leftIndex] <= right[rightIndex]) {
                result[resultIndex] = left[leftIndex]
                leftIndex++
            } else {
                result[resultIndex] = right[rightIndex]
                rightIndex++
            }
            resultIndex++
        }
        while (leftIndex < leftLength) {
            result[resultIndex] = left[leftIndex]
            leftIndex++
            resultIndex++
        }
        while (rightIndex < rightLength) {
            result[resultIndex] = right[rightIndex]
            rightIndex++
            resultIndex++
        }
        return result;
    }
    ```

- **堆排序**
利用堆所设计的一种排序算法。堆积是一个近似完全二叉树的结构，并同时满足堆积的性质：即子结点的键值或索引总是小于（或者大于）它的父节点。
    1. 将初始待排序关键字序列构建成大顶堆。
    2. 将堆顶元素与最后一个元素交换，并弹出最后一个元素至有序区。
    3. 循环 1~2 步骤直至排序完成。
    ```kotlin
    fun heapSort(array: IntArray): IntArray {
        val length = array.size
        val heap = PriorityQueue<Int>(length, { a, b -> b - a })
        array.forEach { heap.add(it) }
        while (heap.size > 0) {
            array[heap.size - 1] = heap.poll()
        }
        return array
    }
    ```

- **计数排序**
计数排序不是基于比较的排序算法，其核心在于将输入的数据值转化为键存储在额外开辟的数组空间中。计数排序要求输入的数据必须是有确定范围的整数。
    1. 找出待排序的数组中最大和最小的元素。
    2. 统计数组中每个值为 i 的元素出现的次数，存入数组 C 的第 i 项。
    3. 反向填充目标数组。
    ```kotlin
    fun countingSort(array: IntArray): IntArray {
        val length = array.size
        val minValue = array.min()
        val maxValue = array.max()
        val offset = minValue
        val countingLength = maxValue - minValue + 1
        val countingArray = IntArray(countingLength)
        for (i in 0 until length) {
            countingArray[array[i] - offset]++
        }
        var resultIndex = 0
        for (i in 0 until countingLength) {
            while (countingArray[i] > 0) {
                array[resultIndex] = i + offset
                resultIndex++
                countingArray[i]--
            }
        }
        return array
    }
    ```

- **桶排序**
桶排序是计数排序的升级版。假设输入数据服从均匀分布，将数据分到有限数量的桶里，每个桶再分别排序。
    1. 设置一个定量的数组当作空桶。
    2. 遍历输入数据，并且把数据一个一个放到对应的桶里去。
    3. 对每个不是空的桶进行排序。
    4. 从不是空的桶里把排好序的数据拼接起来。
    ```kotlin
    fun bucketSort(array: IntArray, bucketSize: Int = 10): IntArray {
        val length = array.size
        val buckets = Array(bucketSize, { LinkedList<Int>() })
        for (i in 0 until length) {
            val bucketIndex = anyHashFunction(array[i])
            buckets[bucketIndex].push(array[i])
        }
        var resultIndex = 0
        for (bucket in buckets) {
            anySortFunction(bucket)
            for (j in 0 until bucket.size) {
                array[resultIndex] = bucket[j]
                resultIndex++
            }
        }
        return array
    }
    ```

- **基数排序**
基数排序是按照低位先排序，然后收集；再按照高位排序，然后再收集；依次类推，直到最高位。有时候有些属性是有优先级顺序的，先按低优先级排序，再按高优先级排序。最后的次序就是高优先级高的在前，高优先级相同的低优先级高的在前。
    1. 取得数组中的最大数，并取得其位数 n，最终排序 n 轮。
    2. 原始数组从最低位开始取每个位组成数组。
    3. 对该数组进行计数排序。
    ```kotlin
    fun radixSort(array: IntArray, radix: Int = 10): IntArray {
        val length = array.size
        val maxValue = array.max()
        var rounds = 1
        var radixInRounds = radix
        while (maxValue > radixInRounds) {
            radixInRounds *= radix
            rounds++
        }
        for (round in 0 until rounds) {
            for (i in 0 until length) {
                var mask = 1
                (0 until round).forEach {
                    mask *= radix
                }
                val radixValue = array[i] / mask % radix
                bucket[radixValue].push(array[i])
            }
            var resultIndex = 0
            for (i in 0 until radix) {
                while (bucket[i].isNotEmpty()) {
                    array[resultIndex] = bucket[i].poll()
                    resultIndex++
                }
            }
        }
        return array
    }
    ```

#### 数组查找
数组查找问题是很基础的问题，大部分查找问题算法的先决条件是数组排序。
- **查询一个数**
与排序息息相关，如果数组是排序的，采用二分搜索复杂度为 *O*(log<sub>2</sub>n)。如果是无序的，遍历查询的复杂度为 *O*(n)。

- **查询第 K 大（小）的数**
与排序息息相关，把数组进行排序（部分排序）后即可取到所需值，例如：
    1. 对数组整体进行排序，直接根据索引找到需要的数。数组排序最理想的复杂度是 *O*(n\*log<sub>2</sub>n)，根据索引找数的复杂度是 *O(1)*，整个操作的复杂度是 *O*(n\*log<sub>2</sub>n)。
    2. 对数组进行选择排序，第 K 次排序后即所需值。时间复杂度为 *O*(n * k)。
    3. 构造大小为 K 的最大（小）堆，遍历数组后取出堆中最小（大）的数。遍历并生成最大（小）堆的复杂度是 *O*(n * log<sub>2</sub>K)，从堆中取出最小（大）的数的复杂度是 *O*(K\*log<sub>2</sub>K)，整个操作的复杂度是 *O*((n + K) * log<sub>2</sub>K)。

- **找出数组中和为目标值的 2 个数**
与排序息息相关，数组是否排序思路不同，例如：
    1. 最直观的方法，无序数组可以直接遍历两遍，复杂度为 *O*(n<sup>2</sup>)
    2. 对数组进行排序，复杂度为 *O*(n\*log<sub>2</sub>n)，排序后生成一个差值数组，差值数组与原数组中都存在的值即为解，寻求解的复杂度为 *O*(n)，整个操作的复杂度为 *O*(n\*log<sub>2</sub>n)。
- **找出数组 {a<sub>1</sub>, a<sub>2</sub>, …, a<sub>n</sub>} 中和为 n 的 K 个数**
把问题分解为 **找出数组 {a<sub>1</sub>, a<sub>2</sub>, …, a<sub>i-1</sub>, a<sub>i+1</sub>, …, a<sub>n</sub>} 中和为（n - a<sub>i</sub>）的 K - 1 个数**，递归求出答案。

- **找出数组中出现次数大于 n/2 的元素**
计数排序后即可得出结果。复杂度为 *O*(n)。

#### 链表相关算法
- **反转单链表**
    ```kotlin
    // 非递归实现（语意更清晰）
    fun reverseLinkedList(node: Node): Node {
        if (node.next == null) return node
        var head: Node? = node
        var previous: Node? = null
        var next: Node?
        while (head != null) {
            next = head.next
            head.next = previous
            previous = head
            head = next
        }
        return previous!!
    }

    // 递归实现
    fun reverseLinkedList(node: Node): Node{
        val next = node.next ?: return node
        node.next = null
        val reverseRest = reverseLinkedList(next)
        next.next = node
        return reverseRest
    }
    ```

- **查询单链表倒数第 n 个元素**
快慢指针，令指针 a 先前进 n 步后，指针 b 开始执行，在指针 a 到达链表尾部时指针 b 指向的即为倒数第 n 个元素。

- **判断链表是否有环**
快慢指针，令指针 a 正常遍历，指针 b 以 2 的间隔遍历，指针 a 与指针 b 在到达链表尾部前相遇即有环。

#### 栈与队列互相实现
可以利用两个栈实现队列，利用两个队列实现栈。假设为 a 和 b。在压入（入列）时把元素压入 a，在弹出（出列）时若 b 有元素则直接从 b 中弹出（出列），若 b 为空则把 a 中元素翻转倒入 b，并从 b 中弹出（出列）。

宽度优先搜索 Breadth First Search
深度优先搜索 Depth First Search
回溯法 Backtracking
动态规划 Dynamic Programming
扫描线 Scan-line algorithm

返回滑动窗口中的最大值

判断有效的字母异位词
字母异位词分组

验证二叉搜索树
二叉树的最近公共祖先
二叉搜索树的最近公共祖先

返回前 K 个高频单词

实现计算 x 的 n 次方幂

计算买卖股票的最佳时机

二叉树层次遍历
返回二叉树最大路径和
找出二叉树的最小深度
生成所有可能的有效括号组合

N 皇后问题
判断数独是否有效
解决数独

实现一个求解平方根的函数
设计并实现一个 LRU 缓存

二维网格中的单词搜索问题

找出字符串中的最长回文
超级洗衣机问题
计算最短编辑距离问题
不同面值硬币兑换问题

计算矩阵中的朋友圈总数

二进制数中比特位统计的问题

[Home](../../README)