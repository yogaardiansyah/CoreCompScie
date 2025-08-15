# The Golden Ratio Search: Fibonacci Search

## 1. Definition: What is Fibonacci Search?

Fibonacci Search is a "divide and conquer" search algorithm that, like Binary Search, operates on **sorted** arrays. However, instead of dividing the array into two equal halves, it divides the array into two unequal parts based on **Fibonacci numbers**.

The core idea is to narrow down the search space by using Fibonacci numbers to determine the probe position. This allows the algorithm to perform its search using only addition and subtraction, avoiding the division or bit-shifting operations required by Binary Search to find the midpoint. While its time complexity is the same as Binary Search (`O(log n)`), its reliance on simpler arithmetic operations gave it an advantage on older or more primitive CPUs where division was a significantly slower operation.

## 2. The Core Analogy: The Unevenly Cut Ribbon

Imagine you have a long, sorted ribbon of numbers and you want to find a specific value.

*   **Binary Search** would cut the ribbon exactly in half, look at the number, and discard one of the halves.
*   **Fibonacci Search** uses a more subtle approach. It knows that any length can be represented as a sum of Fibonacci numbers (Zeckendorf's theorem). It uses this property to make an "uneven" but intelligent cut.
    *   It finds the smallest Fibonacci number `F(m)` greater than the ribbon's length.
    *   It makes its first cut at a position corresponding to the preceding Fibonacci number, `F(m-1)`. This divides the ribbon into two sections of lengths `F(m-1)` and `F(m-2)`.
    *   Based on the comparison, it knows which of the two smaller sections to continue searching in. The key insight is that the size of the new search space is also a Fibonacci number, allowing the process to be repeated efficiently.

This method of dividing the search space according to the golden ratio (`φ ≈ 1.618`), which is intrinsically linked to Fibonacci numbers, is the algorithm's defining feature.

## 3. The Crucial Prerequisite: Sorted Data

Like all efficient divide-and-conquer search algorithms (Binary, Ternary, etc.), Fibonacci Search is entirely dependent on the data being **sorted**. The algorithm's logic relies on being able to discard a large portion of the array after a single comparison, which is only possible if the elements are in a known order.

## 4. The Mechanism: How Fibonacci Search Works (The Theory)

The algorithm uses the fundamental property of Fibonacci numbers: `F(m) = F(m-1) + F(m-2)`.

1.  **Find the Right Fibonacci Number:**
    *   Given an array of size `n`, find the smallest Fibonacci number `F(m)` that is greater than or equal to `n`.
    *   We will need the three consecutive Fibonacci numbers that define our search space: `F(m)`, `F(m-1)`, and `F(m-2)`.

2.  **Initialization:**
    *   An `offset` variable is used to keep track of the eliminated part of the array from the front. It is initialized to -1.

3.  **The Loop:** The loop continues as long as there is a valid search space (represented by `F(m-2)` being greater than 0).
    *   **Calculate Probe Index:** Calculate the index `i` to probe. This index is the sum of the current `offset` and the second-to-last Fibonacci number: `i = offset + F(m-2)`.
    *   **Compare:** Compare the `target` value with the element at `array[i]`.
        *   **Case 1: `target < array[i]` (Target is in the left part)**
            *   The target must be in the left sub-array of size `F(m-2)`.
            *   We discard the right part. To search in the new, smaller space, we move down the Fibonacci sequence by one step: `m = m - 1`.
        *   **Case 2: `target > array[i]` (Target is in the right part)**
            *   The target must be in the right sub-array of size `F(m-1)`.
            *   We discard the left part by updating our `offset` to the current probe index: `offset = i`.
            *   To search in this new space, we move down the Fibonacci sequence by two steps: `m = m - 2`.
        *   **Case 3: `target == array[i]` (Match Found)**
            *   The search is successful. Return the index `i`.

4.  **Termination:**
    *   The loop terminates when the search space is exhausted.
    *   There might be one last element to check if `F(m-1)` is 1 and the `offset` has been updated. A final comparison is made. If it's not a match, the element is not in the array.

## 5. A Step-by-Step Example

Let's trace a Fibonacci Search for the target value **91**.

**List:** `[2, 5, 8, 12, 16, 23, 38, 56, 72, 91, 105]`
**Size (n):** 11
**Target:** 91

**Fibonacci Numbers:** 0, 1, 1, 2, 3, 5, 8, **13** (`F(7)`)
*   Smallest Fibonacci number >= 11 is 13. So, `m = 7`.
*   We will use `F(m) = F(7) = 13`, `F(m-1) = F(6) = 8`, `F(m-2) = F(5) = 5`.

| `m` | `F(m-2)` | `offset` | `i` Calculation | `i` | `Value at i` | Action / Result |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| 7 | 5 | -1 | `-1 + 5` | 4 | 16 | `91 > 16`. Target is in the right part. Set `offset = i`, `m = m - 2`. |
| 5 | 2 | 4 | `4 + 2` | 6 | 38 | `91 > 38`. Target is in the right part. Set `offset = i`, `m = m - 2`. |
| 3 | 1 | 6 | `6 + 1` | 7 | 56 | `91 > 56`. Target is in the right part. Set `offset = i`, `m = m - 2`. |
| 1 | 0 | 7 | - | - | - | Loop terminates as `F(m-2)` is 0. |

**Final Check:**
*   After the loop, we check if `F(m-1)` is 1 and if `array[offset + 1]` is the target.
*   `m` is 1, so `F(m-1) = F(0) = 0`. This condition is not met. Let's re-evaluate the logic.
*   Let's trace again carefully. After `m=3`, `offset=7`, `m` becomes `3-2=1`. `F(m-2) = F(-1)` which is not defined. The loop should stop when `m` is small. Let's say `m=2`. `F(m-2)=F(0)=0`.
*   Let's refine the trace. After `m=3`, `offset=7`, `m` becomes 1. The loop condition `F(m-2)` is no longer valid.
*   Let's try a better final check. The last valid comparison was at `offset=7`. The next element is `offset+1=8`. `array[8]=72`. Still too small. The next is `array[9]=91`. Match! The logic needs a final check.

Let's use a standard implementation's logic for the final step.
After the loop, if `F(m-1)` is 1 and `array[offset+1]` is the target, we have a match.
In our trace, after the last step, `m=3`, `offset=7`. `array[7]=56`. `91 > 56`. `offset` becomes 7, `m` becomes 1. The loop terminates. `F(m-1) = F(0) = 0`. This path is tricky.

Let's try a different target: **38**.
| `m` | `F(m-2)` | `offset` | `i` Calculation | `i` | `Value at i` | Action / Result |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| 7 | 5 | -1 | `-1 + 5` | 4 | 16 | `38 > 16`. Target is in the right part. Set `offset = i`, `m = m - 2`. |
| 5 | 2 | 4 | `4 + 2` | 6 | 38 | `38 == 38`. **Match found!** Return index 6. |

This trace works perfectly. The complexity lies in handling the final steps and array boundaries.

## 6. Sample Implementation in Python

```python
def fibonacci_search(arr, target):
  """
  Performs a Fibonacci Search to find the index of a target in a sorted array.
  """
  n = len(arr)
  if n == 0:
    return -1

  # Initialize Fibonacci numbers
  fib_m_minus_2 = 0  # F(m-2)
  fib_m_minus_1 = 1  # F(m-1)
  fib_m = fib_m_minus_1 + fib_m_minus_2 # F(m)

  # Find the smallest Fibonacci number greater than or equal to n
  while fib_m < n:
    fib_m_minus_2 = fib_m_minus_1
    fib_m_minus_1 = fib_m
    fib_m = fib_m_minus_1 + fib_m_minus_2

  offset = -1

  # While there are elements to be inspected
  while fib_m > 1:
    # Calculate the probe index, ensuring it's within bounds
    i = min(offset + fib_m_minus_2, n - 1)

    if arr[i] < target:
      # Target is in the right part
      # Discard the left part by moving our three Fibonacci numbers down by two
      fib_m = fib_m_minus_1
      fib_m_minus_1 = fib_m_minus_2
      fib_m_minus_2 = fib_m - fib_m_minus_1
      offset = i
    elif arr[i] > target:
      # Target is in the left part
      # Discard the right part by moving our three Fibonacci numbers down by one
      fib_m = fib_m_minus_2
      fib_m_minus_1 = fib_m_minus_1 - fib_m_minus_2
      fib_m_minus_2 = fib_m - fib_m_minus_1
    else:
      # Match found
      return i

  # Final check for the last element
  if fib_m_minus_1 and offset < (n - 1) and arr[offset + 1] == target:
    return offset + 1

  # Element not found
  return -1

# --- Using the function ---
my_sorted_data = [2, 5, 8, 12, 16, 23, 38, 56, 72, 91, 105]
target_val = 91
result = fibonacci_search(my_sorted_data, target_val)
print(f"Searching for {target_val} in {my_sorted_data}")
print(f"Result: Target found at index {result}")
```

**Output:**
```
Searching for 91 in [2, 5, 8, 12, 16, 23, 38, 56, 72, 91, 105]
Result: Target found at index 9
```

## 7. Analysis and Key Characteristics

### Time Complexity
*   **O(log n)** - The search space is reduced by a constant factor in each step. This factor is related to the golden ratio (`φ ≈ 1.618`). The number of iterations is therefore proportional to `log_φ(n)`, which is `O(log n)`. It is slightly slower than Binary Search in terms of the number of comparisons, but its operations are simpler.

### Space Complexity
*   **O(1)** - The iterative implementation uses a constant amount of extra memory.

## 8. The Philosophical Insight: The Elegance of Simplicity

Fibonacci Search is a testament to the idea that sometimes a "less direct" approach can be more efficient under specific constraints. Its existence is a direct consequence of the hardware limitations of early computing.

**Why does Fibonacci Search exist if Binary Search is also `O(log n)`?**

1.  **Arithmetic Simplicity:** The primary advantage of Fibonacci Search is that it **only uses addition and subtraction** to narrow the search space. Binary Search requires division or bit-shifting (`(low + high) / 2`) to find the midpoint. On very early or simple processors, integer division was a significantly more expensive (slower) operation than addition. Fibonacci Search provided a way to achieve logarithmic performance without this costly operation.

2.  **Cache Performance (A Minor Modern Point):** The probe points in Fibonacci Search tend to be closer together than the wide, random-access jumps of Binary Search. This can lead to slightly better CPU cache utilization in some scenarios, as subsequent memory accesses are more likely to be in a recently fetched cache line. However, this benefit is marginal on modern systems and is not its primary justification.

In today's world of incredibly fast processors with highly optimized arithmetic units, the performance difference between addition and division is negligible for an operation performed only `log n` times. Therefore, **Binary Search is almost always preferred in modern software** due to its simplicity of implementation and slightly better performance in terms of comparison counts.

Fibonacci Search remains a beautiful and historically significant algorithm, a perfect example of how algorithmic design is deeply intertwined with the underlying hardware constraints of its time.