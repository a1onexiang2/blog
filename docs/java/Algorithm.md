[Home](../../README.md)
[TOC]

# Java

## Algorithm

#### 排序
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
- **选择排序**<br>
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
- **插入排序**<br>
通过构建有序序列，对于未排序数据，在已排序序列中从后向前扫描，找到相应位置并插入。
    1. 从第一个元素开始，认为该元素已排序。
    2. 取出下一个元素，在已经排序的元素序列中从后向前扫描，并找到合适的位置插入。
    3. 针对未排序序列的所有元素重复 1~2 步骤。
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
- **冒泡排序**<br>
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
- **希尔排序**<br>
插入排序的改进版。它与插入排序的不同之处在于，它会优先比较距离较远的元素。希尔排序又叫缩小增量排序。
    1. 选择一个关于增量的单调递减序列 {t<sub>1</sub>，t<sub>2</sub>，…，t<sub>k</sub>}，其中 t<sub>k</sub>=1。
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
- **快速排序**<br>
每次排序将待排记录分隔成独立的两部分，其中一部分记录的关键字均比另一部分的关键字小，对这两部分记录继续进行排序，以达到整个序列有序。
    1. 从数列中挑出一个元素，作为基准（pivot）。
    2. 重新排序数列，所有元素比基准值小的摆放在前面，所有元素比基准值大的摆在后面，与基准相等的数可以到任一边。
    3. 继续把子数列进行快速排序。
    4. 递归步骤 1~3 直至排序完成。
    ```kotlin
    fun quickSort(array: IntArray, left: Int = 0, right: Int = array.size - 1): IntArray {
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
            quickSort(array, left, i - 1)
        }
        if (right > j + 1) {
            quickSort(array, j + 1, right)
        }
        return array
    }
    ```
- **归并排序**<br>
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
- **堆排序**<br>
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
- **计数排序**<br>
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
- **桶排序**<br>
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
- **基数排序**<br>
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

#### 查找
数组查找问题是很基础的问题，大部分查找问题算法的先决条件是数组排序。
- **查询一个数**<br>
与排序息息相关，如果数组是排序的，采用二分搜索复杂度为 *O*(log<sub>2</sub>n)。如果是无序的，遍历查询的复杂度为 *O*(n)。
- **查询第 K 大（小）的数**<br>
与排序息息相关，把数组进行排序（部分排序）后即可取到所需值，例如：
    1. 对数组整体进行排序，直接根据索引找到需要的数。数组排序最理想的复杂度是 *O*(n\*log<sub>2</sub>n)，根据索引找数的复杂度是 *O*(1)，整个操作的复杂度是 *O*(n\*log<sub>2</sub>n)。
    2. 对数组进行选择排序，第 K 次排序后即所需值。时间复杂度为 *O*(n * k)。
    3. 构造大小为 K 的最大（小）堆，遍历数组后取出堆中最小（大）的数。遍历并生成最大（小）堆的复杂度是 *O*(n * log<sub>2</sub>K)，从堆中取出最小（大）的数的复杂度是 *O*(K\*log<sub>2</sub>K)，整个操作的复杂度是 *O*((n + K) * log<sub>2</sub>K)。
- **找出数组中和为 n 的 2 个数**<br>
与排序息息相关，数组是否排序思路不同，例如：
    1. 最直观的方法，无序数组可以直接遍历两遍，复杂度为 *O*(n<sup>2</sup>)
    2. 对数组进行排序，复杂度为 *O*(n\*log<sub>2</sub>n)，排序后生成一个差值数组，差值数组与原数组中都存在的值即为解，寻求解的复杂度为 *O*(n)，整个操作的复杂度为 *O*(n\*log<sub>2</sub>n)。
- **找出数组 {a<sub>1</sub>, a<sub>2</sub>, …, a<sub>n</sub>} 中和为 n 的 K 个数**<br>
把问题分解为 **找出数组 {a<sub>1</sub>, a<sub>2</sub>, …, a<sub>i-1</sub>, a<sub>i+1</sub>, …, a<sub>n</sub>} 中和为（n - a<sub>i</sub>）的 K - 1 个数**，递归求出答案。
- **找出数组中出现次数大于 n/2 的元素**<br>
计数排序后即可得出结果。复杂度为 *O*(n)。

#### 动态规划
通过拆分问题，定义问题状态和状态之间的关系，使得问题能够以递推（或分治）的方式去解决的一种算法。
本题下的其他答案，大多都是在说递推的求解方法，但如何拆分问题，才是动态规划的核心。
而拆分问题，靠的就是状态的定义和状态转移方程的定义。
- **n 人过桥最短时间**<br>
n 人过桥，只有一个灯，桥每次只能通过 2 个人。
预设耗时已由短至长排序，设 t<sub>k</sub> 为第 k 个人过桥耗时，则 t<sub>0</sub> 表示耗时最短的人过桥时间，t<sub>1</sub> 表示耗时次短的人过桥时间。归纳总结，第 k 人过桥，总耗时最短有两种可能：
    1. k - 1 人已过桥，由 t<sub>0</sub> 送回灯，t<sub>0</sub> 与 t<sub>k</sub> 一同过桥，则 F(k) = F(k-1) + t<sub>0</sub> + t<sub>k</sub>。
    2. k - 2 人已过桥，由 t<sub>0</sub> 送回灯，t<sub>k-1</sub> 与 t<sub>k</sub> 一同过桥，由 t<sub>1</sub> 送回等，t<sub>0</sub> 与 t<sub>1</sub> 一同过桥，则 F(k) = F(k-2) + t<sub>1</sub> * 2 + t<sub>0</sub> + t<sub>k</sub>。

    可推导出 F(n) = Math.min(F(n-1) + t<sub>0</sub> + t<sub>n</sub>, F(n-2) + t<sub>1</sub> * 2 + t<sub>0</sub> + t<sub>n</sub>)
- **最大子串（连续子序列）**<br>
预设数列为 {a<sub>1</sub>, a<sub>2</sub>, …, a<sub>n</sub>}，S(i) 表示 {a<sub>1</sub>, a<sub>2</sub>, …, a<sub>i</sub>} 的最大子序列。归纳总结，在考虑元素 a<sub>k</sub> 时，有两种可能：
    1. 把 a<sub>k</sub> 加上，则 S(k) = S(k-1) + a<sub>k</sub>。
    2. a<sub>k</sub> 自身。

    可推导出S(n) = Math.max(S(n-1) + a<sub>n</sub>, a<sub>n</sub>)。
- **字符串最大公共子序列**<br>
预设两个字符串分别为 {a<sub>1</sub>, a<sub>2</sub>, …, a<sub>m</sub>}，{b<sub>1</sub>, b<sub>2</sub>, …, b<sub>n</sub>}，C(i, j) 表示 {a<sub>1</sub>, a<sub>2</sub>, …, a<sub>i</sub>}，{b<sub>1</sub>, b<sub>2</sub>, …, b<sub>j</sub>} 的公共子序列。归纳总结，C(i, j) 有两种可能：
    1. a<sub>i</sub> 与 b<sub>j</sub> 满足子序列条件，则 C(i, j) = C(i-1, j-1) + 1。
    2. a<sub>i</sub> 与 b<sub>j</sub> 不满足子序列条件，C(i, j) = Math.max(C(i-1, j), C(i, j-1))。

    可推导出
    ![image](https://user-images.githubusercontent.com/8423120/46864848-129f4980-ce4e-11e8-8665-894329f7356a.png)
- **超级洗衣机问题**<br>
有 n 台超级洗衣机放在同一排上。第 k 台洗衣机内有 a<sub>k</sub> 件衣服。在每一步操作中，你可以选择 m（1 ≤ m ≤ n） 台洗衣机，将每台洗衣机的一件衣服送到相邻的一台洗衣机。最少多少步可以使所有洗衣机中的衣服数量相等，如果不能使每台洗衣机中衣物的数量相等，则返回 -1。
预设 {a<sub>1</sub>, a<sub>2</sub>, …, a<sub>n</sub>} 为 n 台洗衣机中衣服的数量，C(k) 表示 k 台洗衣机需要操作的最少次数。归纳总结，每台洗衣机与平均值的差值为 {d<sub>1</sub>, d<sub>2</sub>, …, d<sub>n</sub>}，D(k) 表示 k 台洗衣机与平均值的累积差值和，则对于第 k 台洗衣机而言，有两种可能：
    1. C(k-1) 不如 d<sub>k</sub> 大，处理 a<sub>k</sub> 需要更多的操作次数。
    2. d<sub>k</sub> 的出现使 D(k) 更大，累积需要更多的操作次数。
    3. d<sub>k</sub> 的出现使 D(k) 变小，C(k-1) 已是最多的操作次数。

    可推导出 C(n) = Math.max(Math.max(C(n-1), Math.abs(D(n-1) + d<sub>n</sub>)), d<sub>n</sub>)。

#### 贪婪算法
贪婪即在局部作出在最好的选择。虽然贪心算法不能对所有问题都得到整体最优解，其最终结果却是最优解的很好近似，而且对许多问题它能产生整体最优解。如最短生成图问题，最小生成树问题等。
- **会议安排问题**<br>
设有 n 个活动，每个活动都要使用同一个场地，每个活动都有一个开始 s<sub>i</sub> 和结束时间 f<sub>i</sub>，即他的使用区间为（s<sub>i</sub>,f<sub>i</sub>）, 现要求分配活动占用时间表，要求举办尽可能多地活动。数学证明该问题使用贪心策略得到的解是全局最优解。
贪心策略：根据结束时间排序后，一直选择 f<sub>i</sub> 最小的作为结果元素之一。
- **线段覆盖问题**<br>
设有 n 条线段，每条线段有一个开始位置 s<sub>i</sub> 和结束位置 f<sub>i</sub>，求所有线段整体覆盖的长度。
贪心策略：预设 L(i) 表示前 i 条线段覆盖的总长度，则对于第 i 条线段，若 s<sub>i</sub>>f<sub>i-1</sub>，则说明线段 i 与线段 i-1 不相连或相交，此时线段长度为 L(i) = L(i-1) +  f<sub>i</sub> - s<sub>i</sub>，若 f<sub>i</sub><f<sub>i-1</sub>，说明线段 i-1 包含线段 i，可以不考虑线段 i，此时线段长度为 L(i) = L(i-1)，否则线段 i-1 与 i 部分重合，则 L(i) = L(i-1) + f<sub>i</sub> - f<sub>i-1</sub>。
- **找零钱的问题**<br>
有一个数组 C = {c<sub>1</sub>, c<sub>2</sub>, …, c<sub>n</sub>}，现要从 C 中选出数字（可重复）使数字相加的和正好等于 K，如何分配使得使用的数字最少。
贪心策略：假定 C 是升序的，D(i) 表示选择 i 个数字后与 K 的差，则每次对于 D(i)，选择 C 中最大但不大于 D(i) 的值作为元素之一。
数学证明对于等比数列 C = {c<sub>1</sub> = aq<sup>0</sup>, c<sub>2</sub> = aq<sup>1</sup>, …, c<sub>n</sub> = aq<sup>i</sup>} 时，贪心策略是全局最优策略。

#### 链表相关算法
- **反转单链表**<br>
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
- **查询单链表倒数第 n 个元素**<br>
快慢指针，令指针 a 先前进 n 步后，指针 b 开始执行，在指针 a 到达链表尾部时指针 b 指向的即为倒数第 n 个元素。
- **判断单链表是否有环**<br>
快慢指针，令指针 a 正常遍历，指针 b 以 2 的间隔遍历，指针 a 与指针 b 在到达链表尾部前相遇即有环。
- **单链表环的长度**<br>
快慢指针，假设第一次相遇后位于结点 p，则从 p 结点开始再次用快慢指针，再次相遇时慢指针的遍历数即环的长度。
- **带环链表环的连接点**<br>
快慢指针，假设第一次相遇后位于结点 p，则从 p 结点和头结点同时开始遍历，相遇结点即环的连接点。
- **判断两个无环单链表是否相交**<br>
若无环，尾结点必然是同一结点。
- **判断两个单链表（不知是否有环）是否相交**<br>
先分别判断链表是否有环。若都无环，尾结点相同则相交；若一个有环一个无环则必然不相交；若两者都有环，可以判断 a 中快慢指针的碰撞点是否存在于 b 链表中，存在则相交。
- **寻找两个相交单链表的第一个相交结点**<br>
若无环，人工构造环，将 a 链表尾结点指向 b 链表头结点，并寻找环的连接点。若有环，先计算两个链表的长度，长的链表先前进长度差值步后，短的链表开始遍历，相遇点即第一个相交结点。

#### 栈与队列
- **栈与队列互相实现**<br>
可以利用两个栈实现队列，或利用两个队列实现栈。假设为 a 和 b。在压入（入列）时把元素压入 a，在弹出（出列）时若 b 有元素则直接从 b 中弹出（出列），若 b 为空则把 a 中元素翻转倒入 b，并从 b 中弹出（出列）。
- **括号是否成对**<br>
用栈来实现，遇到开括号就入栈，闭括号就出栈，遍历后栈应该是空的。
- **后置算术表达式求值**<br>
用两个栈，数值栈 a 与运算符栈 b 来实现。
    1. 遇到值时压入 a。
    2. 遇到运算符时，判断当前运算符与 b 栈顶运算符的优先级，若当前运算符的优先级相等或更大，直接压入 b，若当前运算符优先级更小，则执行步骤 3。
    3. 从 a 弹出两个数字，b 弹出符号进行运算，把运算结果压入 a。重复步骤 2 直至当前运算符入栈。
- **栈能否弹出特定数列**<br>
文字表述：
问题：一个栈的输入是数列 A，假定输出是数列 B，如何判断 B 是否能通过入栈出栈操作从 A 转换而来。
解答：假设原索引指 B 中的值在 A 中的索引位置，则对 B 的每一个值，其后所有原索引比他小的值，它们的原索引必须符合降序排序。
数学表述：
问题：若栈的输入是数列 A = {a<sub>1</sub>, a<sub>2</sub>, …,a<sub>n</sub>}，可以在任意时候执行任意次出栈，能否输出数列 B = {b<sub>1</sub>, b<sub>2</sub>, …,b<sub>n</sub>}，其中 b<sub>i</sub>(i=1, 2, …, n)∈A。
解答：对于数列 B 中任意的 b<sub>i</sub>(i=1, 2, …, n-1) = a<sub>k</sub>，需要满足 {b<sub>i+1</sub> = a<sub>x<sub>1</sub></sub>, b<sub>i+2</sub> = a<sub>x<sub>2</sub></sub>,…, b<sub>n</sub> = a<sub>x<sub>n-i</sub></sub>} 中，对于所有的 x<sub>m</sub>\<x<sub>n</sub>\<k，m\>n。
- **n 对括号有多少合法组合方式**<br>
合法组合方式数与有几对括号相关，是一个卡塔兰数。
它的递推公式有两种表示形式，分别是：
![image](https://user-images.githubusercontent.com/8423120/46858634-4b372700-ce3e-11e8-94f8-df9e548225db.png)
![image](https://user-images.githubusercontent.com/8423120/46858630-496d6380-ce3e-11e8-9479-c24c8435eb66.png)
它的一般项公式是：
![image](https://user-images.githubusercontent.com/8423120/46858565-217e0000-ce3e-11e8-86d1-f3e44ec16e38.png)
- **栈输出所有合法数列**<br>
给定一个输入数列依次入栈，栈可以在任意时机执行弹出操作，输出所有合法数列。合法数列的总数与输入数组的长度相关，是一个卡塔兰数。
    ```kotlin
    /**<br>
     * @param source 数据源数组
     * @param index 当前正在处理的数组索引
     * @param input 输入栈，表示已经从输入中压入的元素
     * @param output 输出栈，表示已经从输入栈中弹出的元素
     */
    fun updateStack(source: IntArray, index: Int = 0, input: Stack<Int> = Stack(), ouyput: Stack<Int> = Stack()) {
        // 选择一：新的元素入栈
        input.push(source[index])
        val countInputted = index + 1
        // 如果已输入数组所有成员，达到终止条件
        if (countInputted == source.size) {
            // 输出结果并退出递归
            val result = StringBuilder()
            for (i in 0 until ouyput.size) {
                result.append(ouyput.elementAt(i).toString())
                result.append(", ")
            }
            for (i in input.size - 1 downTo 0) {
                result.append(input.elementAt(i).toString())
                result.append(", ")
            }
            Log.d("stackOutput", result.dropLast(2).toString())
        } else {
            // 新的元素入栈后递归
            inter(source, countInputted, input, ouyput)
        }
        // 还原选择一
        input.pop()
        if (input.isNotEmpty()) {
            // 选择二：已入栈的元素出栈
            ouyput.push(input.pop())
            // 元素出栈后递归
            inter(source, index, input, ouyput)
            // 还原选择二
            if (ouyput.isNotEmpty()) input.push(ouyput.pop())
        }
    }
    ```
#### 字符串
- **旋转字符串**<br>
给定一个长度为 n 的字符串，要求把字符串前面的 m 个字符移动到字符串的尾部。把字符串 “abcdef” 前面的 2 个字符移到尾部得到 “cdefab”。
    1. 直观暴力解，每次循环把第一个字符串移到尾部。时间复杂度为 *O*(m\*n)，空间复杂度为 *O*(1)。
    2. 三步反转，把前 m 个字符反转，把后 n - m 个字符反转，把整个字符串反转。时间复杂度为 *O*(n)，空间复杂度为 *O*(1)。
- **字符串中的最长回文**<br>
若是起始点和终止点已知的回文，可使用两个指针反向遍历。若回文的起始点和终止点未知，可翻转字符串，寻找两个字符串的最长子串。

#### 树
- **树遍历**<br>
树的遍历一般分为：广度优先搜索（BFS）、深度优先搜索（DFS）。DFS 从遍历的顺序分为前序、中序、后序。前序先遍历根结点，然后遍历子树；中序先遍历左（右）子树，然后遍历根，然后遍历右（左）子树；后序遍历先遍历子树，再遍历根。
- **二叉树的最近公共祖先**<br>
    1. 自底向上遍历结点，一旦遇到结点等于 a 或者 b，则将其向上传递给它的父结点。并由父结点判断其左右子树是否都包含其中的一个结点。
    2. 求出两个结点的公共路径，并寻找公共路径的最长子串，或者两个相交单链表的交点。
- **找出二叉树的最大（小）深度**<br>
分别搜索左子树与右子树的深度。
- **返回二叉树最大路径和**<br>
从根节点递归往下搜索，不断求出子树最大路径。
- **验证二叉搜索树是否合法**<br>
使用前序遍历，根据每个结点的位置设定上下界，判断是否符合上下界要求。或者使用中序遍历递归判断。

#### 八皇后、数独
- **八皇后**<br>
    1. 枚举，剪枝（排除对称，排除不可能位置），回溯。
    2. 跳舞链算法。
- **数独答案是否合法**<br>
使用二维数组计数每个数字出现过的次数。若行/列/九宫中出现超过一次则非法。
- **生成数独答案**<br>
    1. 回溯法：枚举，剪枝，回溯。
    2. 分治组合法：以九宫格为单位分治求解，九宫格之间组合，再使用回溯法。
    3. 跳舞链算法。

[Home](../../README.md)
