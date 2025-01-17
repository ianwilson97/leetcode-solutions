# [Leetcode Problem 567: Permutation in String](https://leetcode.com/problems/permutation-in-string/)

## Table of Contents
1. [Approach 1: Generate All Permutations (Brute Force)](#approach-1-generate-all-permutations-brute-force)
2. [Approach 2: Sliding Window with Character Count Comparison](#approach-2-sliding-window-with-character-count-comparison)
3. [Approach 3: Simplified Sliding Window with Counter](#approach-3-simplified-sliding-window-with-counter)
---

## Approach 1: Generate All Permutations (Brute Force)

### Intuition
The simplest approach to solve this problem is to generate all possible permutations of string `s1` and check if any of these permutations is a substring of `s2`. However, generating all permutations is computationally expensive as it involves factorial time complexity.

### Implementation
```python
from itertools import permutations

def checkInclusion(s1: str, s2: str) -> bool:
    # Generate all permutations of s1
    perm = permutations(s1)
    
    # Check if any permutation is a substring of s2
    for p in perm:
        if ''.join(p) in s2:
            return True
    return False

# Example usage
s1 = "ab"
s2 = "eidbaooo"
print(checkInclusion(s1, s2))  # Output: True
```

### Time and Space Complexity
- **Time Complexity**: \(O(n! \times m)\), where \(n\) is the length of `s1`, and \(m\) is the length of `s2`.
- **Space Complexity**: \(O(n!)\), required for storing permutations.

---

## Approach 2: Sliding Window with Character Count Comparison

### Intuition
A more efficient way is to use the sliding window technique combined with character counts. The main idea is to use a fixed-size window that slides over `s2`. The length of this window should be equal to the length of `s1`. For each position of the window:
1. Check if the character frequency matches that of `s1`.
2. If frequencies match, it implies a permutation of `s1` is present in the current window position of `s2`.

### Detailed Steps
1. Initialize character count arrays for `s1` and the first window in `s2`.
2. Slide the window over `s2`:
   - Add the count of the new character entering the window.
   - Subtract the count of the character exiting the window.
   - After updating, compare the count arrays of `s1` and the current window.

### Implementation
```python
def checkInclusion(s1: str, s2: str) -> bool:
    if len(s1) > len(s2):
        return False

    # Initialize count arrays for characters
    s1_count = [0] * 26
    s2_count = [0] * 26

    # Fill the count arrays for s1 and the first window in s2
    for i in range(len(s1)):
        s1_count[ord(s1[i]) - ord('a')] += 1
        s2_count[ord(s2[i]) - ord('a')] += 1
    
    # Helper function to compare two count arrays
    def matches(s1_count, s2_count):
        for i in range(26):
            if s1_count[i] != s2_count[i]:
                return False
        return True

    # Slide through s2
    for i in range(len(s1), len(s2)):
        if matches(s1_count, s2_count):
            return True
        # Include a new character in the window
        s2_count[ord(s2[i]) - ord('a')] += 1
        # Exclude the oldest character from the window
        s2_count[ord(s2[i - len(s1)]) - ord('a')] -= 1

    # Check the last window after the loop
    return matches(s1_count, s2_count)

# Example usage
s1 = "ab"
s2 = "eidbaooo"
print(checkInclusion(s1, s2))  # Output: True
```

### Time and Space Complexity
- **Time Complexity**: \(O(l + m)\), where \(l\) is the length of `s1`, and \(m\) is the length of `s2`. Iterating once through `s2` and performing constant-time operations for each character.
- **Space Complexity**: \(O(1)\), the space needed for the frequency arrays is constant.

## Approach 3: Simplified Sliding Window with Counter
### Intuition

This problem asks to find if a string contains a permutation of another string. The key insight is that permutations have identical character frequencies, and we can use a sliding window to efficiently check each possible substring without recounting characters each time.

### Approach

1.  First check if s1 is longer than s2 - if so, no permutation can exist
2.  Create character frequency maps:
    -   Count characters in pattern string (s1)
    -   Count characters in first window of s2
3.  Check if first window matches pattern
4.  Slide window through remaining text:
    -   Add new character on right
    -   Remove leftmost character
    -   Remove character from count if frequency becomes zero
    -   Compare window with pattern
5.  Return true if any window matches, false if none match

### Code
```python
from collections import Counter

def check_inclusion(s1: str, s2: str) -> bool:
    """
    Check if s2 contains a permutation of s1.
    """
    # Edge case: s1 longer than s2
    if len(s1) > len(s2):
        return False
        
    # Initialize frequency counters
    pattern_count = Counter(s1)
    window_count = Counter(s2[:len(s1)])
    
    # Check first window
    if pattern_count == window_count:
        return True
    
    # Slide window through remaining string
    for i in range(len(s1), len(s2)):
        # Update window counts
        window_count[s2[i]] += 1
        window_count[s2[i - len(s1)]] -= 1
        
        # Remove zero counts
        if window_count[s2[i - len(s1)]] == 0:
            del window_count[s2[i - len(s1)]]
            
        # Check for permutation
        if pattern_count == window_count:
            return True
            
    return False
```

### Complexity

-   Time complexity: $$O(n)$$ where n is length of s2
    -   Single pass through s2
    -   Counter comparisons are O(1) since limited by alphabet size
-   Space complexity: $$O(1)$$
    -   Two Counter objects storing at most 26 characters each (lowercase English letters)
    -   Fixed size regardless of input length
