# [Leetcode 48: Rotate Image](https://leetcode.com/problems/rotate-image/)

## Approaches
- [Approach 1: Transpose and Reverse (Basic Solution)](#approach-1-transpose-and-reverse-basic-solution)
- [Approach 2: Cycle Swap (Optimized Solution)](#approach-2-cycle-swap-optimized-solution)

### Approach 1: Transpose and Reverse (Basic Solution)
The key idea behind this approach is to recognize that rotating a matrix by 90 degrees clockwise is equivalent to taking the transpose of the matrix and then reversing each row.

**Intuition:**
1. **Transpose the Matrix:** Swap the elements at positions (i, j) with (j, i) for all i and j. This effectively flips the matrix over its diagonal.
2. **Reverse Each Row:** Simply reverse the order of elements in each row to complete the 90-degree clockwise rotation.

**Steps:**
1. Iterate over the matrix and swap: `matrix[i][j]` with `matrix[j][i]` to transpose the matrix.
2. Reverse each row for the final rotated matrix.

Time Complexity: O(N^2), where N is the number of rows/columns in the matrix.
Space Complexity: O(1), since we are modifying the matrix in-place.

```python
def rotate(matrix):
    n = len(matrix)
    
    # Step 1: Transpose the matrix.
    for i in range(n):
        for j in range(i, n):
            # Swap elements to transpose the matrix
            matrix[i][j], matrix[j][i] = matrix[j][i], matrix[i][j]
    
    # Step 2: Reverse each row.
    for i in range(n):
        # Reverse this row
        matrix[i].reverse()

# Example usage:
# matrix = [
#   [1, 2, 3],
#   [4, 5, 6],
#   [7, 8, 9]
# ]
# rotate(matrix)
# Now, matrix = [
#   [7, 4, 1],
#   [8, 5, 2],
#   [9, 6, 3]
# ]
```

### Approach 2: Cycle Swap (Optimized Solution)
This approach involves rotating the matrix layer by layer or cycle by cycle, performing the swaps in-place without explicitly transposing and reversing.

**Intuition:**
1. Visualize the matrix as concentric layers or cycles.
2. For each cycle, perform a series of swaps to rotate the elements to their correct positions.

**Steps:**
1. The outer loop traverses each layer starting from the outermost.
2. For each element in a given layer, perform four-way swaps: top -> right -> bottom -> left -> back to top.

Time Complexity: O(N^2), where N is the number of rows/columns in the matrix.
Space Complexity: O(1), as the rotations are done in-place.

```python
```python
def rotate_matrix_90_degrees(matrix: list) -> None:
    matrix_size = len(matrix)
    
    # Rotate layer by layer from outer to inner
    for current_layer in range(matrix_size // 2):
        layer_start = current_layer
        layer_end = matrix_size - 1 - current_layer
        
        for element_idx in range(layer_start, layer_end):
            position_offset = element_idx - layer_start
            
            # Store the top element before overwriting
            temp_top = matrix[layer_start][element_idx]
            
            # Move left element to top
            matrix[layer_start][element_idx] = matrix[layer_end - position_offset][layer_start]
            
            # Move bottom element to left
            matrix[layer_end - position_offset][layer_start] = matrix[layer_end][layer_end - position_offset]
            
            # Move right element to bottom
            matrix[layer_end][layer_end - position_offset] = matrix[element_idx][layer_end]
            
            # Move stored top element to right
            matrix[element_idx][layer_end] = temp_top

# Example usage:
# matrix = [
#   [1, 2, 3],
#   [4, 5, 6],
#   [7, 8, 9]
# ]
# rotate(matrix)
# Now, matrix = [
#   [7, 4, 1],
#   [8, 5, 2],
#   [9, 6, 3]
# ]
```

Both approaches accomplish the task of rotating the image in-place, each presenting an intuitive yet different methodology to solving this classic problem. Approach 2 might be slightly more efficient due to not requiring explicit row reversals and may avoid unnecessary operations.

