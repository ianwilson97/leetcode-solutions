# Leetcode Problem: [792. Number of Matching Subsequences](https://leetcode.com/problems/number-of-matching-subsequences/)

## Approaches
1. [Brute Force With Two Pointers](#approach-1-brute-force-with-two-pointers)
2. [Frequency with Maps](#approach-2-frequency-with-maps)
3. [Indexed Characters with Binary Search (Optimal)](#approach-3-indexed-characters-with-binary-search-optimal)
4. [Iterative String Pattern Matching](#approach-4-iterative-string-pattern-matching)
---

## Approach 1: Brute Force With Two Pointers

### Intuition:
The simplest method to solve this problem is to check each word in the list to see if it is a subsequence of the given string `s` by using two pointers. If a word is a subsequence of `s`, increment a count.

### Steps:
- For each word in the list, use two pointers: one for `s` and one for the word.
- Traverse string `s` with the first pointer. For each character in `s`, check if it matches the current character of the word using the second pointer.
- If the characters match, move the pointer for the word.
- If the pointer for the word reaches the end of the word, it is a subsequence.
 
### Code:
```python
def numMatchingSubseq(s, words):
    def isSubsequence(word, s):
        # Pointer for the word
        i = 0
        # Traverse through the characters in s
        for char in s:
            if i < len(word) and word[i] == char:
                # Move word pointer on match
                i += 1
            if i == len(word):
                return True
        return i == len(word)
    
    count = 0
    for word in words:
        if isSubsequence(word, s):
            count += 1
    return count
```

### Complexity:
- **Time Complexity:** O(n * m), where `n` is the length of the input string `s`, and `m` is the accumulated length of words in the list.
- **Space Complexity:** O(1) (apart from input storage).

---

## Approach 2: Frequency with Maps

### Intuition:
Instead of scanning the entire string `s` for each word, store the positions of each character in `s`. For each word, check if its characters appear in increasing order using the stored positions.

### Steps:
- Maintain a dictionary where each character in `s` maps to a list of its indices in the string.
- For each word, attempt to match each character using the stored indices, ensuring the indices are strictly increasing with each character match.

### Code:
```python
from collections import defaultdict
from bisect import bisect_left

def numMatchingSubseq(s, words):
    char_indices = defaultdict(list)
    
    # Populate the mapping of characters to their indices
    for index, char in enumerate(s):
        char_indices[char].append(index)
    
    count = 0
    for word in words:
        prev_index = -1
        found = True
        
        for char in word:
            if char not in char_indices:
                found = False
                break
            
            # Use binary search to find an index of char that is greater than prev_index
            next_pos = bisect_left(char_indices[char], prev_index + 1)
            if next_pos == len(char_indices[char]):
                found = False
                break
            
            # Update prev_index to the index just found
            prev_index = char_indices[char][next_pos]
        
        if found:
            count += 1
    return count
```

### Complexity:
- **Time Complexity:** O(n + sum(len(word) * log m)), where `n` is the length of string `s` and `m` is the average frequency of the most common characters in `s`.
- **Space Complexity:** O(m + n), where `m` is the distinct character count in `s`.

---

## Approach 3: Indexed Characters with Binary Search (Optimal)

### Intuition:
Combine the mapping of indices from `s` with binary search to efficiently determine the presence and order of word characters within `s`.

### Steps:
- Build a dictionary with indices for each character in `s` as seen previously.
- Use binary search to efficiently find the proper indices for characters from each word in the given map.

### Code:
```python
from collections import defaultdict
from bisect import bisect_left

def numMatchingSubseq(s, words):
    char_indices = defaultdict(list)
    
    # Store character positions
    for index, char in enumerate(s):
        char_indices[char].append(index)
    
    def isSubsequence(word):
        prev_position = -1
        for char in word:
            if char not in char_indices:
                return False
            
            # Find the smallest index greater than prev_position
            idx = bisect_left(char_indices[char], prev_position + 1)
            if idx == len(char_indices[char]):
                return False
            
            prev_position = char_indices[char][idx]
        
        return True
    
    return sum(isSubsequence(word) for word in words)
```

### Complexity:
- **Time Complexity:** O(n + w * log n), where `n` is the length of `s` and `w` is the total number of words.
- **Space Complexity:** O(n), for the character-to-indices mapping of `s`.

## Approach 4
### **Intuition**
  When we first look at this problem, we need to understand what makes a string a subsequence of another. A subsequence is formed by taking characters from the original string while keeping their relative order, but they don't need to be consecutive. For example, "ace" is a subsequence of "abcde" because we can find 'a', 'c', and 'e' in order, even though they're not adjacent.

The natural first thought is to check each word against the main string by trying to find each character in order. We want to ensure that after finding each character, we only look forward in the main string for the next character, never backward, to maintain the proper sequence.

### **Approach**  
The solution implements an elegant way to check for subsequences using Python's built-in string methods. Here's how it works:

1.  The main function  `numMatchingSubseq`  takes two parameters:
    -   `s`: The source string to check against
    -   `words`: A list of words to check if they are subsequences
2.  The nested helper function  `isSubsequence`  does the heavy lifting:
    -   It maintains a  `current_position`  pointer that starts at -1
    -   For each character in the word we're checking:
        -   We use  `s.find(char, current_position + 1)`  to look for the next occurrence of the character
        -   The second parameter in  `find()`  tells it to start searching from  `current_position + 1`, ensuring we only move forward
        -   If we can't find the character (`current_position == -1`), the word is not a subsequence
    -   If we successfully find all characters in order, the word is a subsequence
3.  The main function then:
    -   Initializes a counter for matching words
    -   Checks each word using  `isSubsequence`
    -   Increments the counter for each matching word
    -   Returns the final count
```python
class Solution:
    def numMatchingSubseq(self, s: str, words: List[str]) -> int:
        def isSubsequence(word: str) -> bool:
            current_position = -1
            for char in word:
                current_position = s.find(char, current_position + 1)
                if current_position == -1:
                    return False
            return True

        matching_words = 0
        for word in words:
            if isSubsequence(word):
                matching_words += 1
        return matching_words
```
### **Complexity**

-   Time complexity: $$O(k * n)$$ Where k is the total length of all words combined, and n is the length of the source string s. For each character in each word, we might need to scan through a portion of the source string.
-   Space complexity: $$O(1)$$ We only use a constant amount of extra space regardless of input size. The  `current_position`  variable and loop counters are the only extra space needed, and they don't grow with input size.
