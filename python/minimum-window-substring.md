# [LeetCode Problem 76: Minimum Window Substring](https://leetcode.com/problems/minimum-window-substring/)

## Approaches
- [Brute Force Approach](#brute-force-approach)
- [Sliding Window Approach](#sliding-window-approach)

## Brute Force Approach

### Intuition
The brute force approach involves trying every possible substring in `s` and checking if it contains all characters of `t`. If it does, you compare the length of this substring with the current minimum length and update it accordingly. This approach is straightforward but inefficient due to its exhaustive nature.

### Approach
1. Iterate over all possible starting points of substrings in `s`.
2. For each starting point, iterate to form substrings and check if they contain all characters in `t`.
3. Count the occurrences of each character in the substrings and in `t`.
4. Compare the substring length to find the minimal substring meeting the conditions.

### Code
```python
def minWindow(s: str, t: str) -> str:
    if not t or not s:
        return ""

    def contains_all(s_count, t_count):
        for char in t_count:
            if s_count.get(char, 0) < t_count[char]:
                return False
        return True

    min_length = float('inf')
    min_window = ""
    t_count = {}
    
    # Count occurrences of each character in t
    for char in t:
        t_count[char] = t_count.get(char, 0) + 1

    for start in range(len(s)):
        s_count = {}
        for end in range(start, len(s)):
            char = s[end]
            s_count[char] = s_count.get(char, 0) + 1
            if contains_all(s_count, t_count):
                window_length = end - start + 1
                if window_length < min_length:
                    min_length = window_length
                    min_window = s[start:end + 1]
                break

    return min_window
```

### Complexity
- **Time Complexity**: \(O(n^3)\), where \(n\) is the length of the string `s`. This complexity arises because for every start position, we are traversing the string and counting characters.
- **Space Complexity**: \(O(n + m)\), where \(n\) is the size of `s` and `m` is the size of `t`.

## Sliding Window Approach

### Intuition
The sliding window approach is a more optimal solution. The idea is to use two pointers to create a window within the string `s` that can be adjusted as we look for the minimum length window that contains all characters in `t`. As characters are added to the window, we check if all required characters are included; if they are, we try to shrink the window to find a smaller valid window.

### Approach
1. Use two pointers, `left` and `right`, both starting at the beginning of `s`.
2. Expand the window by moving `right` and tracking characters.
3. Once a valid window is found (when all characters in `t` are present), attempt to shrink it by moving `left`.
4. Keep track of the minimum window found.

### Code
```python
class Solution:
    def minWindow(self, s: str, t: str) -> str:
        # Handle edge cases
        if not s or not t:
            return ""
        
        # Create variables for counting
        t_count = Counter(t)
        current_count = {}
        required = len(t_count)
        formed = 0

        # Create variables for sliding window
        left = right = 0
        minimum_length = float('inf')
        minimum_window = (0,0)

        # Main loop for sliding window
        while right < len(s):
            # 1. Extract the char and update current_count
            char = s[right]
            current_count[char] = current_count.get(char, 0) + 1

            # Update if valid is formed
            if char in t_count and current_count[char] == t_count[char]:
                formed += 1
            
            # 2. Reduce the size of window if still valid
            while left <= right and formed == required:
                # Update minimum_length and minimum_window
                current_length = right - left + 1
                if current_length < minimum_length:
                    minimum_length = current_length
                    minimum_window = (left,right)
                
                # Extract the leftmost char from current_count
                char = s[left]
                current_count[char] -= 1

                # Check if removing char invalidates window
                if char in t_count and current_count[char] < t_count[char]:
                    formed -= 1
                
                left += 1
            right += 1
        
        # Extract the minimum window substring
        start, end = minimum_window
        return s[start:end + 1] if minimum_length != float('inf') else ""
```

### Complexity
- **Time Complexity**: \(O(n + m)\), where \(n\) is the length of `s` and \(m\) is the length of `t`. This is because we only have two pointers that traverse the string once.
- **Space Complexity**: \(O(m + k)\), where \(m\) is the number of unique characters in `t`, and \(k\) is the number of unique characters in `s`. This is due to the `Counter` dictionaries used.

