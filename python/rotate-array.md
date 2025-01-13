# [189. Rotate Array](https://leetcode.com/problems/rotate-array/)

## Navigation
- [Approach 1: Brute Force](#approach-1-brute-force)
- [Approach 2: Using Extra Array](#approach-2-using-extra-array)
- [Approach 3: Reverse Array](#approach-3-reverse-array)
- [Approach 4: Two-Point Slice Swap](#approach-4-slice-swap)

## Approach 1: Brute Force

### Intuition
The simplest method to rotate an array by `k` steps to the right involves rotating the array one step at a time, `k` times. Each single rotation involves moving the last element of the array to the first position and shifting the rest of the elements to the right by one position.

### Code
```python
def rotate(nums, k):
    n = len(nums)
    k %= n  # In case k is greater than n
    for _ in range(k):
        # Rotate array by one step
        last = nums[-1]
        for i in range(n-1, 0, -1):
            nums[i] = nums[i-1]
        nums[0] = last
```

### Complexity Analysis
- **Time Complexity**: \(O(n \times k)\), as we are performing a single rotation `k` times.
- **Space Complexity**: \(O(1)\), because no additional space is used.

---

## Approach 2: Using Extra Array

### Intuition
A more efficient solution is to create a new array and then place each element of the original array in its new position. We can create a new array, and then for each element at index `i` in the array, place it at the index `(i + k) % n` in the new array.

### Code
```python
def rotate(nums, k):
    n = len(nums)
    k %= n  # In case k is greater than n
    rotated = [0] * n
    for i in range(n):
        # Place each element in its new position
        rotated[(i + k) % n] = nums[i]
    # Copy the rotated array back to nums
    nums[:] = rotated
```

### Complexity Analysis
- **Time Complexity**: \(O(n)\), as each element is put into its new position.
- **Space Complexity**: \(O(n)\), as we are using an additional array of size `n`.

---

## Approach 3: Reverse Array

### Intuition
A more optimal approach involves reversing parts of the array to achieve the rotation. First, reverse the entire array, then reverse the first `k` elements, and finally reverse the remaining `n-k` elements.

### Steps
1. Reverse the entire array.
2. Reverse the first `k` elements.
3. Reverse the remaining `n-k` elements.

Reversal of parts allows elements to "jump" to their target locations in constant time, making the array rotation efficient.

### Code
```python
def rotate(nums, k):
    n = len(nums)
    k %= n  # In case k is greater than n
    
    # Helper function to reverse part of the array
    def reverse(start, end):
        while start < end:
            nums[start], nums[end] = nums[end], nums[start]
            start += 1
            end -= 1

    # 1. Reverse the entire array
    reverse(0, n - 1)
    
    # 2. Reverse the first k elements
    reverse(0, k - 1)
    
    # 3. Reverse the remaining n-k elements
    reverse(k, n - 1)

```

### Complexity Analysis
- **Time Complexity**: \(O(n)\), as we perform three reversals, each taking \(O(n)\).
- **Space Complexity**: \(O(1)\), as the reversal is done in place without any additional storage need.

Each approach improves efficiency from the one before, with the third approach being optimal for both time and space.

## Approach 4: Two-Point Python Slice Swap

### Intuition

When rotating an array by k positions to the right, we're essentially splitting the array into two parts and swapping their positions. The last k elements move to the front, while the remaining elements shift to the end.

### Approach

1.  First, we handle edge cases:
    -   If k is larger than array length, we only need to rotate by k % len(nums)
    -   If array has 0-1 elements or k = 0, no rotation needed
2.  The main logic uses Python's slice assignment feature:
    -   `nums[-k:]`  takes the last k elements
    -   `nums[:-k]`  takes all elements except the last k
    -   We assign these slices to  `nums[:k]`  (first k positions) and  `nums[k:]`  (remaining positions) respectively

For example, with nums = [1,2,3,4,5] and k = 2:

-   `nums[-k:]`  is [4,5]
-   `nums[:-k]`  is [1,2,3]
-   After assignment: [4,5,1,2,3]

```python
class Solution:
    def rotate(self, nums: List[int], k: int) -> None:
        """
        Do not return anything, modify nums in-place instead.
        """
        k = k % len(nums)

        if len(nums) <= 1 or k == 0:
            return

        nums[:k], nums[k:] = nums[-k:], nums[:-k]
```

### Complexity

-   Time complexity: $$O(n)$$
    -   Creating slices and copying elements requires traversing the array once
-   Space complexity: $$O(1)$$
    -   While Python's slice assignment creates temporary lists internally, the problem considers this as constant space since we're using language features rather than explicitly creating additional data structures
