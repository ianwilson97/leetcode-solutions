# [Leetcode Problem 4: Median of Two Sorted Arrays](https://leetcode.com/problems/median-of-two-sorted-arrays/)

## Approaches

1. [Brute Force Approach](#brute-force-approach)
2. [Optimal Approach (Binary Search)](#optimal-approach-binary-search)

---

### Brute Force Approach

**Intuition:**

The most straightforward solution is to merge the two arrays first, then find the median from the merged array. Although this method is not optimal in terms of time complexity, it provides a base understanding of how to combine the two datasets and find the median from them.

**Steps:**

1. Merge the arrays while maintaining order.
2. Find the median of the merged array.

```python
def findMedianSortedArrays(nums1, nums2):
    # Merge two arrays
    merged = sorted(nums1 + nums2)
    length = len(merged)

    # If the length is odd, return the middle element
    if length % 2 == 1:
        return merged[length // 2]
    
    # If the length is even, return the average of the two middle elements
    return (merged[length // 2 - 1] + merged[length // 2]) / 2.0

```

**Time Complexity:** O((m+n) log(m+n)) - due to the sorting step.

**Space Complexity:** O(m+n) - for storing the merged array.

---

### Optimal Approach (Binary Search)

**Intuition:**

To improve efficiency, we use binary search to find the median without fully merging both arrays. The idea is to partition both arrays into two halves, such that all elements on the left are less than or equal to all elements on the right. We ensure these partitions are balanced in terms of size, ensuring the median can be directly calculated from the limited partitioned elements.

**Steps:**

1. Identify shorter array to partition it, ensuring minimized complexity.
2. Use binary search to find a suitable partition in both arrays.
3. Check conditions, and adjust the partitions according to the binary search outcome.

```python
def findMedianSortedArrays(nums1, nums2):
    # Ensure nums1 is the smaller array
    if len(nums1) > len(nums2):
        nums1, nums2 = nums2, nums1

    x, y = len(nums1), len(nums2)
    low, high = 0, x
    
    while low <= high:
        partitionX = (low + high) // 2
        partitionY = (x + y + 1) // 2 - partitionX

        # If partitionX is 0 it means nothing is there on left side. Use -inf for maxLeftX
        # If partitionX is length of input then there is nothing on right side. Use +inf for minRightX
        maxLeftX = float('-inf') if partitionX == 0 else nums1[partitionX - 1]
        minRightX = float('inf') if partitionX == x else nums1[partitionX]

        maxLeftY = float('-inf') if partitionY == 0 else nums2[partitionY - 1]
        minRightY = float('inf') if partitionY == y else nums2[partitionY]

        # We have partitioned arrays at correct places
        if maxLeftX <= minRightY and maxLeftY <= minRightX:
            # If total number of elements is even
            if (x + y) % 2 == 0:
                return (max(maxLeftX, maxLeftY) + min(minRightX, minRightY)) / 2
            else:
                return max(maxLeftX, maxLeftY)
        elif maxLeftX > minRightY:
            # Move towards left on nums1
            high = partitionX - 1
        else:
            # Move towards right on nums1
            low = partitionX + 1

```

**Time Complexity:** O(log(min(m, n))) - due to binary search only on the smaller array.

**Space Complexity:** O(1) - no extra space used except for variables. 

This optimal solution efficiently finds the median without merging, using the properties of arrays and partitions.

### Optimal Binary Search
```python
class Solution:
    def find_median_sorted_arrays(self, nums1, nums2):
        """
        Find the median of two sorted arrays using binary search.
        
        Args:
            nums1 (List[int]): First sorted array
            nums2 (List[int]): Second sorted array
            
        Returns:
            float: Median value of the combined sorted arrays
        """
        # Make nums1 the smaller array for efficiency
        if len(nums1) > len(nums2):
            return self.find_median_sorted_arrays(nums2, nums1)
        
        size1, size2 = len(nums1), len(nums2)
        total_size = size1 + size2
        partition_size = (total_size + 1) // 2
        
        left, right = 0, size1
        
        while left <= right:
            partition1 = (left + right) // 2
            partition2 = partition_size - partition1
            
            # Get partition elements, using sentinel values for edge cases
            left1 = nums1[partition1 - 1] if partition1 > 0 else float('-inf')
            left2 = nums2[partition2 - 1] if partition2 > 0 else float('-inf')
            right1 = nums1[partition1] if partition1 < size1 else float('inf')
            right2 = nums2[partition2] if partition2 < size2 else float('inf')
            
            if left1 <= right2 and left2 <= right1:
                # Found the correct partition
                if total_size % 2:
                    return max(left1, left2)
                return (max(left1, left2) + min(right1, right2)) / 2
                
            if left1 > right2:
                right = partition1 - 1  # Move partition1 leftward
            else:
                left = partition1 + 1   # Move partition1 rightward
```
