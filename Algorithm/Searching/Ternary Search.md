# The Three-Way Split: Ternary Search

## 1. Definition: What is Ternary Search?

Ternary Search is a "divide and conquer" search algorithm that can be used to find the minimum or maximum of a **unimodal function** within a given interval. While it can also be adapted to search for a specific value in a sorted array, its primary and most effective application is in optimization problems involving unimodal functions.

Unlike Binary Search, which divides the search space into two halves, Ternary Search divides the search space into **three parts** and then eliminates one-third of the space in each iteration. This makes it a powerful tool for finding extrema (minimums or maximums) where the function's behavior changes direction only once.

## 2. The Core Analogy: Finding the Peak of a Mountain in the Dark

Imagine you are trying to find the highest point (the peak) of a mountain range in the dark. You can only measure your current altitude.

*   **Linear Search:** Walking one step at a time, measuring altitude, until you start going down. Very slow.
*   **Binary Search (not directly applicable):** Binary search is for finding a *specific value* in a sorted list, not for finding an extremum of a function.
*   **Ternary Search:**
    1.  You pick two points, `mid1` and `mid2`, roughly dividing your current section of the mountain into three equal parts.
    2.  You measure the altitude at `mid1` and `mid2`.
    3.  **If `altitude(mid1) < altitude(mid2)`:** This means the peak must be to the right of `mid1` (since `mid2` is higher). You can safely discard the leftmost third of your current section.
    4.  **If `altitude(mid1) > altitude(mid2)`:** This means the peak must be to the left of `mid2` (since `mid1` is higher). You can safely discard the rightmost third.
    5.  **If `altitude(mid1) == altitude(mid2)`:** The peak could be between `mid1` and `mid2`, or on either side. You can discard either the leftmost or rightmost third (or both, if you're careful).
    6.  You repeat this process on the remaining two-thirds of the section, continuously narrowing down the location of the peak.

This process of comparing two interior points to eliminate one-third of the search space is the essence of Ternary Search for optimization.

## 3. The Crucial Prerequisite: Unimodal Functions

Ternary Search's effectiveness hinges entirely on the property of **unimodality**.

A function `f(x)` is **unimodal** on an interval `[a, b]` if:
*   It has a single peak (maximum) or a single valley (minimum) within that interval.
*   It is strictly increasing up to the extremum and strictly decreasing afterwards (for a maximum).
*   It is strictly decreasing up to the extremum and strictly increasing afterwards (for a minimum).

If a function has multiple peaks/valleys or is monotonic (always increasing/decreasing), Ternary Search will not reliably find the global extremum.

## 4. The Mechanism: How Ternary Search Works (The Theory)

The algorithm maintains a search interval `[low, high]`. In each step, it calculates two internal points, `mid1` and `mid2`, which divide the interval into three roughly equal parts.

1.  **Initialization:**
    *   Define the search interval `[low, high]`.
    *   Define a small `epsilon` value for floating-point comparisons, or a maximum number of iterations for integer ranges, to determine when to stop.

2.  **The Loop:** Continue as long as `high - low` is greater than `epsilon` (or `high >= low` for integer search, with a final check).
    *   **Calculate `mid1` and `mid2`:**
        *   `mid1 = low + (high - low) / 3`
        *   `mid2 = high - (high - low) / 3` (or `mid2 = mid1 + (high - low) / 3`)
        *   *Note:* For integer arrays, ensure `mid1` and `mid2` are distinct and within bounds.

    *   **Compare Function Values:** Evaluate the function `f(x)` at `mid1` and `mid2`.
        *   **For finding a MAXIMUM:**
            *   If `f(mid1) < f(mid2)`: The maximum must be in the right two-thirds. Set `low = mid1`.
            *   Else (`f(mid1) >= f(mid2)`): The maximum must be in the left two-thirds. Set `high = mid2`.
        *   **For finding a MINIMUM:**
            *   If `f(mid1) > f(mid2)`: The minimum must be in the right two-thirds. Set `low = mid1`.
            *   Else (`f(mid1) <= f(mid2)`): The minimum must be in the left two-thirds. Set `high = mid2`.

3.  **Termination:** When the interval `[low, high]` becomes sufficiently small, the extremum is considered to be at `low` (or `high`, or `(low+high)/2`).

## 5. A Step-by-Step Example (Finding Maximum)

Let's find the maximum of the unimodal function `f(x) = -x^2 + 10x` in the interval `[0, 10]`. The actual maximum is at `x=5`.

**Interval:** `[0, 10]`
**Function:** `f(x) = -x^2 + 10x`
**Epsilon:** 0.01 (for demonstration, we'll stop when `high - low` is small)

| Iteration | `low` | `high` | `mid1` | `mid2` | `f(mid1)` | `f(mid2)` | Action | New Interval |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| 1 | 0 | 10 | `0 + (10-0)/3` = 3.33 | `10 - (10-0)/3` = 6.67 | `f(3.33)` = 22.21 | `f(6.67)` = 22.21 | `f(mid1) == f(mid2)`. Set `high = mid2`. | `[0, 6.67]` |
| 2 | 0 | 6.67 | `0 + (6.67-0)/3` = 2.22 | `6.67 - (6.67-0)/3` = 4.45 | `f(2.22)` = 17.28 | `f(4.45)` = 24.70 | `f(mid1) < f(mid2)`. Set `low = mid1`. | `[2.22, 6.67]` |
| 3 | 2.22 | 6.67 | `2.22 + (6.67-2.22)/3` = 3.71 | `6.67 - (6.67-2.22)/3` = 5.18 | `f(3.71)` = 23.36 | `f(5.18)` = 24.99 | `f(mid1) < f(mid2)`. Set `low = mid1`. | `[3.71, 6.67]` |
| 4 | 3.71 | 6.67 | `3.71 + (6.67-3.71)/3` = 4.69 | `6.67 - (6.67-3.71)/3` = 5.70 | `f(4.69)` = 24.90 | `f(5.70)` = 24.51 | `f(mid1) > f(mid2)`. Set `high = mid2`. | `[3.71, 5.70]` |
| ... | ... | ... | ... | ... | ... | ... | ... | ... |
| **Converge** | ~4.99 | ~5.01 | - | - | - | - | `high - low < epsilon`. Stop. | `[4.99, 5.01]` |

The algorithm quickly converges on the interval `[4.99, 5.01]`, indicating the maximum is around `x=5`.

## 6. Sample Implementation in Python (Finding Maximum)

```python
def f(x):
  """Example unimodal function: -x^2 + 10x (parabola opening downwards)"""
  return -x**2 + 10*x

def ternary_search_max(low, high, epsilon=1e-7):
  """
  Finds the maximum of a unimodal function f(x) within [low, high]
  using Ternary Search.
  """
  while (high - low) > epsilon:
    mid1 = low + (high - low) / 3
    mid2 = high - (high - low) / 3

    if f(mid1) < f(mid2):
      # Max is in the right two-thirds
      low = mid1
    else:
      # Max is in the left two-thirds (or mid1 == mid2)
      high = mid2
      
  return (low + high) / 2 # Return the approximate maximum x-value

# --- Using the function ---
start_interval = 0
end_interval = 10
max_x = ternary_search_max(start_interval, end_interval)
print(f"Function: f(x) = -x^2 + 10x")
print(f"Interval: [{start_interval}, {end_interval}]")
print(f"Approximate x-value for maximum: {max_x:.5f}")
print(f"Maximum value of f(x): {f(max_x):.5f}")

# --- Example for finding a value in a sorted array (less common use) ---
def ternary_search_value(arr, target):
  """
  Finds a target value in a sorted array using Ternary Search.
  (Less efficient than Binary Search for this specific task).
  """
  low = 0
  high = len(arr) - 1

  while low <= high:
    # Ensure mid1 and mid2 are distinct and within bounds
    if high - low < 2: # If interval is too small, fall back to linear check
        if arr[low] == target: return low
        if arr[high] == target: return high
        return -1

    mid1 = low + (high - low) // 3
    mid2 = high - (high - low) // 3

    if arr[mid1] == target:
      return mid1
    if arr[mid2] == target:
      return mid2

    if target < arr[mid1]:
      high = mid1 - 1
    elif target > arr[mid2]:
      low = mid2 + 1
    else: # target is between arr[mid1] and arr[mid2]
      low = mid1 + 1
      high = mid2 - 1
      
  return -1

my_sorted_array = [10, 20, 30, 40, 50, 60, 70, 80, 90, 100]
target_val = 70
result_val = ternary_search_value(my_sorted_array, target_val)
print(f"\nSearching for {target_val} in {my_sorted_array}")
print(f"Result: Target found at index {result_val}")
```

**Output:**
```
Function: f(x) = -x^2 + 10x
Interval: [0, 10]
Approximate x-value for maximum: 5.00000
Maximum value of f(x): 25.00000

Searching for 70 in [10, 20, 30, 40, 50, 60, 70, 80, 90, 100]
Result: Target found at index 6
```

## 7. Analysis and Key Characteristics

### Time Complexity
*   **O(log₃ n)** or **O(log n)** - In each step, Ternary Search reduces the search space by one-third. This means it takes `log₃ n` iterations. Since `log₃ n = log₂ n / log₂ 3`, its complexity is still `O(log n)` in Big O notation (as constant factors are ignored).
    *   **Comparison to Binary Search:** Binary Search reduces the space by half, taking `log₂ n` iterations. Since `log₂ 3 ≈ 1.58`, Ternary Search performs roughly `1.58` times more iterations than Binary Search to achieve the same reduction in search space. However, each iteration of Ternary Search involves two function evaluations (or two comparisons for array search), whereas Binary Search involves only one. This means Ternary Search is generally **slower** than Binary Search for finding a specific value in a sorted array.

### Space Complexity
*   **O(1)** - For the iterative implementation, memory usage is constant.

## 8. The Philosophical Insight: The Niche of Optimization

Ternary Search's existence highlights a crucial aspect of algorithm design: **specialization**. While it's generally less efficient than Binary Search for simple value lookup in sorted arrays, it excels in its specific niche: **optimization problems on unimodal functions.**

**When to Use Ternary Search:**

1.  **Finding Extrema of Unimodal Functions:** This is its primary and most effective use case. If you have a function `f(x)` that you know is unimodal (has one peak or one valley) within a given range, and you need to find the `x` value where that extremum occurs, Ternary Search is a powerful tool. This is common in competitive programming and certain mathematical optimization contexts.
2.  **When Function Evaluation is Expensive:** If evaluating `f(x)` is computationally very expensive, Ternary Search might be considered. However, the fact that it requires two evaluations per step often makes it less attractive than other optimization methods unless the function is truly unimodal and well-behaved.

For general-purpose searching in sorted arrays, **Binary Search remains the superior choice** due to its fewer comparisons per iteration and faster convergence rate. Ternary Search is a specialized algorithm for a specific class of optimization problems.