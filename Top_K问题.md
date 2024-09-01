### Top_K问题

> **$K-selection:$** 在任意一组可比较大小的元素中，如何由小到大，找到次序为$k$的元素？(第k大元素)

- heap实现

```py
import heapq

def top_k_num(nums, k):
    res = []
    heapq.heapify(res)
    for num in nums:
        heapq.heappush(res, num)
        if len(res) > k:
            heapq.heappop(res)
    return res[0]
```

- 快排实现

```py
import random

def find_kth_largest(nums, k):
    def partition(left, right):
        pivot_idx = random.randint(left, right)
        pivot = nums[pivot_idx]
        nums[pivot_idx], nums[right] = nums[right], nums[pivot_idx]
        store_idx = left
        for i in range(left, right):
            if nums[i] > pivot:
                nums[i], nums[store_idx] = nums[store_idx], nums[i]
                store_idx += 1
        # 数组倒排，大的在左，小的在右
        nums[right], nums[store_idx] = nums[store_idx], nums[right]
        return store_idx

    def select(left, right):
        if left == right:
            return nums[left]
        pivot_idx = partition(left, right)
        if pivot_idx == k - 1:
            return nums[pivot_idx]
        elif pivot_idx > k - 1:
            return select(left, pivot_idx - 1)
        else:
            return select(pivot_idx + 1, right)

    return select(0, len(nums) - 1)    
```

### 众数

定义：如果一个数组元素中，某个元素出现的次数超过了一半以上，那么我们称这个元素为这组元素的众数。否则，众数不存在。

> 每次取出两个不同的数，剩下的数字中重复出现的数字肯定比其他数字多，将规模缩小化。如果每次删除两个不同的数（不管包括不包括最高频数），那么在剩余的数字里，原最高频数出现的频率一样超过了50%，不断重复这个过程，最后剩下的将全是同样的数字，即最高频数。此算法避免了排序，时间复杂度只有O(n)，空间复杂度为O(1)。

```python
def find_majority(nums):
    count = 0
    for num in nums:
        if count == 0:
            maj = num
        else:
            if maj == num:
                count += 1
            else:
                count -= 1
    return maj

if __name__ == '__main__':
    nums = [1, 2, 4, 2, 3, 2, 2]
    maj = find_majority(nums)
    count = 0
    for num in nums:
        if num == maj:
            count += 1
    if count > len(nums) // 2:
        return True
    else:
        return False       
```

### 中位数

<img src="https://markdown-1258220306.cos.ap-shenzhen-fsi.myqcloud.com/img/202409011813961.png" title="" alt="" width="559">

- 无序数组找中位数

> 知道数组长度之后，可以转换为$top\_K$问题

- 两个有序数组的中位数

```py
class Solution:
    def findMedianSortedArrays(self, nums1: List[int], nums2: List[int]) -> float:
        m = len(nums1)
        n = len(nums2)
        if m > n:
            nums1, nums2 = nums2, nums1
            m, n = n, m
        total_left = (m + n + 1) // 2
        left, right = 0, m

        while left < right:
            i = left + (right - left + 1) // 2
            j = total_left - i
            if nums1[i-1] > nums2[j]:
                right = i - 1
            else:
                left = i

        i = left 
        j = total_left - i

        nums1_left_max = float('-inf') if i == 0 else nums1[i-1]
        nums1_right_min = float('inf') if i == m else nums1[i]
        nums2_left_max = float('-inf') if j == 0 else nums2[j-1]
        nums2_right_min = float('inf') if j == n else nums2[j]

        if (m + n) % 2:
            return max(nums1_left_max, nums2_left_max)
        else:
            return (max(nums1_left_max, nums2_left_max) + 
                min(nums1_right_min, nums2_right_min)) / 2
```

- 数据流的中位数

```py
import heapq

class MedianFinder:
    def __init__(self):
        self.small = []  # 大顶堆（用负值实现的小顶堆）
        self.large = []  # 小顶堆

    def add_num(self, num):
        # 将新元素插入大顶堆，然后把大顶堆的堆顶元素移动到小顶堆
        heapq.heappush(self.small, -num)
        heapq.heappush(self.large, -heapq.heappop(self.small))

        # 保证大顶堆的大小不小于小顶堆
        if len(self.small) < len(self.large):
            heapq.heappush(self.small, -heapq.heappop(self.large))

    def find_median(self):
        if len(self.small) > len(self.large):
            return -self.small[0]
        else:
            return (-self.small[0] + self.large[0]) / 2

# 使用示例
finder = MedianFinder()
nums = [7, 1, 3, 4, 9, 2]

for num in nums:
    finder.add_num(num)
    print(f"Current median: {finder.find_median()}")
```
