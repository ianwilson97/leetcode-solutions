# [Leetcode 125: Valid Palindrome](https://leetcode.com/problems/valid-palindrome/)

## Approaches
1. [Two-Pointer Approach with String Builder](#approach-1)
2. [Optimized Two-Pointer Approach (Without Extra Space)](#approach-2)
3. [Single Pass String Cleaning](#approach-3)

## Approach 1: Two-Pointer Approach with String Builder

### Intuition

The essence of this problem is to check if a given string, after cleaning up non-alphanumeric characters, is a palindrome. A palindrome reads the same forward and backward. The simplest way to solve this is by using two pointers. We can optimize this approach by building a cleaned version of the string and then use two pointers to compare characters from both ends of this cleaned string.

### Algorithm
1. Initialize an empty string or list to store alphanumeric characters in lowercase.
2. Traverse the given string:
   - For each character, check if it is alphanumeric.
   - If it is, convert it to lowercase and append to the list.
3. Initialize two pointers: one at the start (`left`) and one at the end (`right`) of the list.
4. Loop while `left` is less than `right`:
   - Check if the characters at `left` and `right` are the same.
   - If not, return `False` because it's not a palindrome.
   - Otherwise, move the `left` pointer to the right and `right` to the left.
5. If loop exits without returning `False`, return `True`.

### Code
```python
def isPalindrome(s: str) -> bool:
    # Create a list to hold only alphanumeric characters in lowercase form
    filtered_chars = [char.lower() for char in s if char.isalnum()]
    
    # Initialize two pointers
    left, right = 0, len(filtered_chars) - 1
    
    # Use two pointers to check palindrome
    while left < right:
        # Compare characters and move pointers
        if filtered_chars[left] != filtered_chars[right]:
            return False
        left, right = left + 1, right - 1
    
    return True
```

### Time Complexity
- O(n): We traverse the string once to filter characters and once again with two pointers.
  
### Space Complexity
- O(n): In the worst case, all characters are stored in the filtered list.

## Approach 2: Optimized Two-Pointer Approach (Without Extra Space)

### Intuition

The previous approach uses extra memory to construct a filtered version of the string. We can optimize this by managing the pointers directly on the original string, skipping non-alphanumeric characters and comparing characters in lower case as we go.

### Algorithm
1. Initialize two pointers: `left` at the beginning and `right` at the end of the string.
2. Loop while `left` is less than `right`:
   - Move the `left` pointer forward if the current character is not alphanumeric.
   - Move the `right` pointer backward if the current character is not alphanumeric.
   - If both characters are alphanumeric, check if they are equal disregarding case.
   - If they are not equal, return `False`.
   - If they are equal, move both pointers inward.
3. If the loop exits, return `True`.

### Code
```python
def isPalindrome(s: str) -> bool:
    # Initialize two pointers
    left, right = 0, len(s) - 1
    
    while left < right:
        # Move left pointer if not alphanumeric
        while left < right and not s[left].isalnum():
            left += 1
        # Move right pointer if not alphanumeric
        while left < right and not s[right].isalnum():
            right -= 1
        
        # Compare characters in a case-insensitive manner
        if s[left].lower() != s[right].lower():
            return False
        
        # Move both pointers towards the center
        left += 1
        right -= 1
        
    return True
```

### Time Complexity
- O(n): Each character is processed at most twice.

### Space Complexity
- O(1): No additional space is used besides a few variables.

## Single-Pass String Cleaning

### Intuition

When thinking about palindromes, we need to focus only on the actual letters and numbers in the string, ignoring spaces, punctuation, and case sensitivity. My first thought was that we need to "clean" the string before checking if it reads the same forwards and backwards. Think of how "A man, a plan, a canal: Panama" is a palindrome - we need to transform it to "amanaplanacanalpanama" before we can properly check.

### Approach

Let's break this down into a clear step-by-step process:

1.  First, we convert the entire string to lowercase using  `s.lower()`. This ensures that cases don't affect our comparison - for example, "A" and "a" should be considered the same character.
2.  Next, we filter out all non-alphanumeric characters using Python's built-in  `filter()`  function with  `str.isalnum`. This removes spaces, punctuation, and any other special characters. The  `isalnum()`  method returns True only for letters and numbers.
3.  We join the filtered characters back into a single string using  `"".join()`. At this point, our string contains only lowercase letters and numbers.
4.  Finally, we compare this cleaned string with its reverse. In Python,  `s[::-1]`  creates a reversed copy of the string using slice notation. If the string equals its reverse, it's a palindrome.

```python
class Solution:
    def isPalindrome(self, s: str) -> bool:
        s = "".join(filter(str.isalnum, s.lower()))
        return s == s[::-1]
```

### Complexity

-   Time complexity: $$O(n)$$ The solution needs to process each character in the input string once during the lowercase conversion, once during filtering, and once during the reverse comparison. While we perform multiple passes, this still results in linear time complexity.
-   Space complexity: $$O(n)$$ We create a new string containing the filtered characters. In the worst case (when all characters are alphanumeric), this will be the same length as the input string. The reversed string also takes up additional space, though Python's slice operations are optimized to minimize memory usage.
