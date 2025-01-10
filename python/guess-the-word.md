# [Leetcode 843: Guess the Word](https://leetcode.com/problems/guess-the-word/)

## Approaches:
- [Basic Approach: Random Selection](#random-selection)
- [Optimal Approach: Minimax with Minimax-Solution](#minimax)
- [Simple Approach: Similarity-Based Elimination](#elimination)

## Basic Approach: Random Selection

### Intuition
The basic approach to solve this problem is by making random guesses from the list of provided words until the correct one is found. This approach does not leverage any useful information from previous guesses, which is why it's considered very naive.

### Approach
1. Randomly select a word from `wordlist` to guess.
2. Pass the selected word to the `guess` function which checks how many characters match with the secret word.
3. Remove the guessed word from `wordlist` and repeat the process until the function confirms the correct guess.

### Code
```python
import random

def basic_guess_the_word(wordlist, guess):
    while wordlist:
        # Randomly select a word to guess
        selected_word = random.choice(wordlist)
        
        # Get how many characters matched
        matches = guess(selected_word)
        
        # If all characters match, we break
        if matches == len(selected_word):
            break
        
        # Remove the selected word from wordlist
        wordlist.remove(selected_word)
```

### Complexity Analysis
- **Time Complexity:** O(N^2) in the worst case, where N is the number of words. Each guess might take linear time if guess removes guessed words.
- **Space Complexity:** O(1), no extra space other than input.

## Optimal Approach: Minimax with Minimax-Solution

### Intuition
Instead of randomly guessing a word, we can use a minimax strategy optimized for this problem. The idea is to minimize the maximum number of possible candidates in the wordlist for the next guess. By doing so, we can reduce the number of guesses needed to find the secret word.

### Approach
1. For each word, calculate the number of possible words that could be the secret word if it matches the given number of characters with the guessed word.
2. Choose the word which minimizes the possible candidates after the guess.
3. Only keep words in `wordlist` which have the same number of matching characters as the guessed word.
4. Repeat the process until the correct word is guessed.

### Code
```python
def optimal_guess(wordlist, guess):
    def match(w1, w2):
        """ Calculate how many characters are the same """
        return sum(c1 == c2 for c1, c2 in zip(w1, w2))
    
    while True:
        # Calculate minimax counts for each word
        count_candidates = []
        for word in wordlist:
            count = [0] * 7  # holds up to 6 matches
            for w in wordlist:
                if word != w:
                    matches = match(word, w)
                    count[matches] += 1
            
            # Minimize the maximum number of same-match words
            count_candidates.append((max(count), word))
        
        # Choose the word with minimal maximum number of same-match candidates
        _, best_word = min(count_candidates)
        
        # Guess this best word
        matches = guess(best_word)
        
        if matches == len(best_word):
            break
        
        # Reduce the wordlist to only probable candidates
        wordlist = [w for w in wordlist if match(w, best_word) == matches]
```

### Complexity Analysis
- **Time Complexity:** O(N^2 * M), where N is the number of words and M is the word length. Each iteration through the list checks matches.
- **Space Complexity:** O(1), adjusting in-place without extra space beyond fixed size calculations.

This approach ensures we minimize our chances of making an incorrect guess and quickly narrows down the list of potential secret words.

## Simple Approach: Similarity-Based Elimination

### Intuition

Imagine playing a game like Wordle where you need to guess a secret word, but instead of getting letter-by-letter feedback, you only get told how many letters are correct in their exact positions. The key insight is that if we make a guess and get told "2 letters are correct," then the secret word MUST share exactly 2 letters in the same positions with our guess – no more, no less. We can use this to narrow down possibilities.

Another crucial insight is that in a large list of words, when we make a random guess, we're more likely to get 0 matches than any other number. So, we want to make guesses that are "typical" or "representative" of the word list, which will help us eliminate more words when we get 0 matches.

### Approach

Our strategy works like a game of 20 questions, but played very strategically:

1.  First, we analyze how common each letter is at each position across all words. For example, if many words have 'a' as their first letter, we consider 'a' "heavy" in the first position. We do this using a Counter for each position (0-5) in the words.
2.  For each word, we calculate its "similarity score" to the rest of the list by adding up the weights of its letters in each position. A higher score means this word shares more common patterns with other words.
3.  We sort words by this similarity score, putting the most "typical" words at the end of our list. We'll use these typical words as our guesses because:
    -   If they get 0 matches (common case), we can eliminate many similar words
    -   If they get some matches (rare case), we have a good reference point for finding the answer
4.  For each guess:
    -   Take the most typical remaining word
    -   Get the number of matching positions from master.guess()
    -   Keep only words that have exactly that many matches with our guess
    -   Repeat until we find the answer

```python
# """
# This is Master's API interface.
# You should not implement it, or speculate about its implementation
# """
# class Master:
#     def guess(self, word: str) -> int:

class Solution:
    def findSecretWord(self, words: List[str], master: 'Master') -> None:
        # Calculate letter frequencies for each position in the word
        letter_frequencies = [Counter(word[position] for word in words) 
                            for position in range(6)]
        
        # Sort words by their similarity score (sum of letter frequencies)
        words.sort(key=lambda current_word: sum(
            letter_frequencies[position][letter] 
            for position, letter in enumerate(current_word))
        )
        
        # Continue guessing until we find the secret word or run out of words
        while words:
            # Pick the most representative word from our remaining list
            candidate_word = words.pop()
            
            # Get number of matching positions from the Master API
            matching_positions = master.guess(candidate_word)
            
            # Filter word list to only keep words with exact same number of matches
            words = [
                remaining_word
                for remaining_word in words
                if matching_positions == sum(
                    word_char == other_char 
                    for word_char, other_char in zip(candidate_word, remaining_word)
                )
            ]
```

### Complexity

-   Time complexity:  `O(n²)`  where n is the number of words
    -   For each guess (up to n times), we might need to check every remaining word
    -   Each word comparison is O(1) since words are fixed length (6)
-   Space complexity:  `O(1)`
    -   The weights list is constant size (6 positions)
    -   We modify the input words list in-place for filtering
