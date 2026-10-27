# The Complete LeetCode Python Handbook
**Data Structures, Algorithm Patterns & Reusable Templates**

A practical reference for coding interviews. Keep this open while solving problems. You do not need to memorise everything immediately.

---

# PART 1 — CORE DATA STRUCTURES

## 1. Arrays & Lists

**Use for:** indexing, sorting, collecting values, two pointers, sliding windows.

### Basic operations
```python
arr = [1, 2, 3]

arr[i]             # Access element
arr[i] = 10        # Update element
arr.append(x)      # Add to end
arr.pop()          # Remove last element
arr.pop(i)         # Remove at index
arr.insert(i, x)   # Insert at index
len(arr)           # Length
arr.sort()         # Sort in place
sorted(arr)        # Return a sorted copy
arr[::-1]          # Reversed copy
arr[1:3]           # Slice
```

### Template: iterate through an array
```python
for i in range(len(nums)):
    print(nums[i])

for num in nums:
    print(num)

for i, num in enumerate(nums):
    print(i, num)
```

### Template: build a result
```python
result = []

for num in nums:
    if condition:
        result.append(num)

return result
```

### Template: accumulate a result
```python
total = 0

for num in nums:
    total += num

return total
```

> **Remember:** Lists are mutable. `.append()` changes the existing list.

---

## 2. Hash Maps / Dictionaries

**Use for:** frequency counting, fast lookups, grouping, remembering previously seen values.

### Basic operations
```python
d = {}

d[key] = value
d[key]                 # Read; raises KeyError if absent
d.get(key, 0)          # Read with default
key in d               # Check key exists
del d[key]             # Delete key
d.items()              # Iterate over key-value pairs
```

### Template: frequency counter
```python
counts = {}

for x in nums:
    counts[x] = counts.get(x, 0) + 1

return counts
```

Or use `Counter`:
```python
from collections import Counter

counts = Counter(nums)
```

### Template: remember previously seen values
Classic pattern for Two Sum:
```python
def twoSum(nums, target):
    seen = {}

    for i, num in enumerate(nums):
        complement = target - num

        if complement in seen:
            return [seen[complement], i]

        seen[num] = i
```

> **Pattern:** Check whether you have seen what you need before storing the current value.

---

## 3. Hash Sets

**Use for:** uniqueness, membership checks, visited nodes, detecting cycles.

### Basic operations
```python
seen = set()

seen.add(x)
seen.remove(x)       # Errors if missing
seen.discard(x)      # Safe if missing
x in seen
x not in seen
len(seen)
```

### Template: detect duplicates
```python
seen = set()

for num in nums:
    if num in seen:
        return True

    seen.add(num)

return False
```

### Template: visited nodes
```python
visited = set()

if node not in visited:
    visited.add(node)
    # Process node
```

> **Important:** Add an item to `visited` at the correct time. For BFS, usually mark it when adding it to the queue.

---

## 4. Stacks

**Use for:** matching parentheses, DFS, undo operations, monotonic stacks.

A stack is **LIFO**: last in, first out.

### Basic operations
```python
stack = []

stack.append(x)      # Push
stack.pop()          # Pop top
stack[-1]            # Peek at top
len(stack)
if not stack:        # Empty?
    ...
```

### Template: matching parentheses
```python
def isValid(s):
    stack = []
    pairs = {')': '(', ']': '[', '}': '{'}

    for ch in s:
        if ch in '([{':
            stack.append(ch)
        else:
            if not stack or stack[-1] != pairs[ch]:
                return False
            stack.pop()

    return not stack
```

### Template: monotonic stack
For problems asking for the next greater element:
```python
def nextGreater(nums):
    result = [-1] * len(nums)
    stack = []  # Indices

    for i, num in enumerate(nums):
        while stack and nums[stack[-1]] < num:
            j = stack.pop()
            result[j] = num

        stack.append(i)

    return result
```

> **Pattern:** Maintain a stack of unresolved indices. When the current value resolves an earlier index, pop it.

---

## 5. Queues & Deques

**Use for:** BFS, level-order traversal, processing items in arrival order.

A queue is **FIFO**: first in, first out.

```python
from collections import deque

q = deque()

q.append(x)          # Add to back
q.popleft()          # Remove from front
q[0]                 # Peek at front
len(q)
```

### Template: BFS
```python
from collections import deque

def bfs(start):
    q = deque([start])
    visited = {start}

    while q:
        node = q.popleft()

        # Process node

        for nei in graph[node]:
            if nei not in visited:
                visited.add(nei)
                q.append(nei)
```

> **Critical detail:** Mark nodes visited when enqueuing them, not after processing them, to prevent duplicate queue entries.

---

## 6. Linked Lists

A linked list uses nodes connected by pointers.
```python
class ListNode:
    def __init__(self, val=0, next=None):
        self.val = val
        self.next = next
```

### Basic operations
```python
node.val
node.next
node.next = other
node = node.next
```

### Template: traverse a linked list
```python
def traverse(head):
    curr = head

    while curr:
        print(curr.val)
        curr = curr.next
```

### Template: reverse a linked list
One of the most important LeetCode patterns:
```python
def reverseList(head):
    prev = None
    curr = head

    while curr:
        nxt = curr.next
        curr.next = prev
        prev = curr
        curr = nxt

    return prev
```

> **Pointer rule:** Save `curr.next` before changing the link, or you lose access to the rest of the list.

### Template: fast and slow pointers
Useful for finding the middle node:
```python
def middleNode(head):
    slow = fast = head

    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next

    return slow
```

### Template: dummy node
Useful for constructing a new linked list:
```python
def buildList(head):
    dummy = ListNode(0)
    tail = dummy

    # Example: append a new node
    tail.next = ListNode(5)
    tail = tail.next

    return dummy.next
```

> The dummy node avoids special handling when the result's first node changes.

---

## 7. Binary Trees

A typical LeetCode node looks like this:
```python
class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right
```

### Basic operations
```python
root.val
root.left
root.right

root.left = TreeNode(5)
root.right = TreeNode(10)
```

> There is no `.append()` for ordinary binary trees. You connect nodes using `.left` and `.right`.

### Template: recursive DFS
```python
def dfs(root):
    if not root:
        return

    # Process current node
    print(root.val)

    dfs(root.left)
    dfs(root.right)
```

### Template: calculate maximum depth
```python
def maxDepth(root):
    if not root:
        return 0

    left = maxDepth(root.left)
    right = maxDepth(root.right)

    return 1 + max(left, right)
```

> **Base case:** An empty tree has depth zero when depth is measured in nodes.

### Template: tree BFS / level-order traversal
```python
from collections import deque

def levelOrder(root):
    if not root:
        return []

    result = []
    q = deque([root])

    while q:
        level = []

        for _ in range(len(q)):
            node = q.popleft()
            level.append(node.val)

            if node.left:
                q.append(node.left)

            if node.right:
                q.append(node.right)

        result.append(level)

    return result
```

> **Pattern:** `len(q)` captures the number of nodes at the current level.

### Template: compare two trees
```python
def isSameTree(p, q):
    if not p and not q:
        return True

    if not p or not q:
        return False

    if p.val != q.val:
        return False

    return (
        isSameTree(p.left, q.left)
        and isSameTree(p.right, q.right)
    )
```

### Template: invert a binary tree
```python
def invertTree(root):
    if not root:
        return None

    root.left, root.right = root.right, root.left

    invertTree(root.left)
    invertTree(root.right)

    return root
```

### Tree recursion checklist
1. What should an empty node return?
2. Do I need the left subtree, right subtree, or both?
3. What should I do with the current node?
4. How do I combine the recursive results?
5. What should I return to the parent call?

---

## 8. Binary Search Trees (BSTs)

A BST follows an ordering rule: values in the left subtree are smaller and values in the right subtree are larger, assuming distinct values under the usual convention.

### Template: search
```python
def searchBST(root, val):
    if not root:
        return None

    if root.val == val:
        return root

    if val < root.val:
        return searchBST(root.left, val)

    return searchBST(root.right, val)
```

### Template: validate a BST
```python
def isValidBST(root):
    def dfs(node, low, high):
        if not node:
            return True

        if not (low < node.val < high):
            return False

        return (
            dfs(node.left, low, node.val)
            and dfs(node.right, node.val, high)
        )

    return dfs(root, float('-inf'), float('inf'))
```

> **Pattern:** Pass constraints down the tree. Checking only a node against its immediate children is insufficient.

---

## 9. Heaps / Priority Queues

**Use for:** top K elements, repeatedly retrieving the minimum or maximum, merging sorted sequences.

Python's `heapq` is a **min-heap** by default.
```python
import heapq

heap = []

heapq.heappush(heap, x)
smallest = heapq.heappop(heap)
heap[0]                      # Peek at minimum
heapq.heapify(nums)          # Convert list to heap
```

### Template: keep the K largest values
```python
import heapq

def kLargest(nums, k):
    heap = []

    for num in nums:
        heapq.heappush(heap, num)

        if len(heap) > k:
            heapq.heappop(heap)

    return heap
```

> The heap keeps at most `k` elements. Its root is the smallest of those retained.

### Max-heap pattern
For numeric values, negate them:
```python
heapq.heappush(heap, -x)
largest = -heapq.heappop(heap)
```

---

## 10. Graphs

Graphs consist of nodes and edges. They may contain cycles, so track visited nodes when appropriate.

### Template: adjacency list
```python
graph = {
    0: [1, 2],
    1: [0, 3],
    2: [0],
    3: [1]
}
```

For an edge list:
```python
from collections import defaultdict

graph = defaultdict(list)

for u, v in edges:
    graph[u].append(v)
    graph[v].append(u)  # For an undirected graph
```

> For a directed graph, only add `u -> v` if that's the direction given.

### Template: graph DFS
```python
def dfs(node, graph, visited):
    if node in visited:
        return

    visited.add(node)

    for nei in graph[node]:
        dfs(nei, graph, visited)
```

### Template: count connected components
```python
from collections import defaultdict

def countComponents(n, edges):
    graph = defaultdict(list)

    for u, v in edges:
        graph[u].append(v)
        graph[v].append(u)

    visited = set()
    components = 0

    for node in range(n):
        if node not in visited:
            components += 1
            dfs(node, graph, visited)

    return components

def dfs(node, graph, visited):
    if node in visited:
        return

    visited.add(node)

    for nei in graph[node]:
        dfs(nei, graph, visited)
```

> **Pattern:** Each new DFS started from an unvisited node identifies another connected component.

---

## 11. Grids / Matrices

**Use for:** Number of Islands, flood fill, shortest paths in a grid.

### Template: four directions
```python
directions = [
    (0, 1),
    (0, -1),
    (1, 0),
    (-1, 0)
]
```

### Template: grid DFS
```python
def dfs(r, c):
    if not (0 <= r < rows and 0 <= c < cols):
        return

    if grid[r][c] != '1':
        return

    if (r, c) in visited:
        return

    visited.add((r, c))

    for dr, dc in directions:
        dfs(r + dr, c + dc)
```

### Template: grid BFS
```python
from collections import deque

def bfs(sr, sc):
    q = deque([(sr, sc)])
    visited.add((sr, sc))

    while q:
        r, c = q.popleft()

        for dr, dc in directions:
            nr, nc = r + dr, c + dc

            if not (0 <= nr < rows and 0 <= nc < cols):
                continue

            if grid[nr][nc] != '1':
                continue

            if (nr, nc) in visited:
                continue

            visited.add((nr, nc))
            q.append((nr, nc))
```

> **Pattern:** Every cell is a node, and neighboring cells are connected by edges.

---

# PART 2 — MAJOR ALGORITHM PATTERNS

## 12. Two Pointers

**Use for:** sorted arrays, pairs, reversing, palindrome checks.

### Template: pointers from both ends
```python
left, right = 0, len(nums) - 1

while left < right:
    # Examine nums[left] and nums[right]

    if condition:
        left += 1
    else:
        right -= 1
```

Example: check whether a string is a palindrome.
```python
def isPalindrome(s):
    left, right = 0, len(s) - 1

    while left < right:
        if s[left] != s[right]:
            return False

        left += 1
        right -= 1

    return True
```

> **Pattern:** Move the pointer that helps you get closer to satisfying the condition.

---

## 13. Sliding Window

**Use for:** contiguous subarrays or substrings.

### Template: variable-size window
```python
left = 0

for right in range(len(nums)):
    # Add nums[right] to the window

    while window_is_invalid:
        # Remove nums[left] from the window
        left += 1

    # Update the answer
```

Example: longest substring without repeating characters.
```python
def lengthOfLongestSubstring(s):
    seen = set()
    left = 0
    best = 0

    for right in range(len(s)):
        while s[right] in seen:
            seen.remove(s[left])
            left += 1

        seen.add(s[right])
        best = max(best, right - left + 1)

    return best
```

> **Pattern:** Expand the right side; shrink the left side until the window is valid.

> **Note:** this standard shrinking-window pattern depends on the problem having a condition that can be maintained as the window moves.

---

## 14. Binary Search

**Use for:** sorted arrays or a monotonic condition.

### Template: find a target
```python
def binarySearch(nums, target):
    left, right = 0, len(nums) - 1

    while left <= right:
        mid = (left + right) // 2

        if nums[mid] == target:
            return mid

        if nums[mid] < target:
            left = mid + 1
        else:
            right = mid - 1

    return -1
```

### Template: first position where a condition is true
For a monotonic condition that changes from false to true:
```python
left, right = low, high

while left < right:
    mid = (left + right) // 2

    if condition(mid):
        right = mid
    else:
        left = mid + 1

return left
```

> **Pattern:** Decide which half can still contain the answer, then discard the other half.

---

## 15. Backtracking

**Use for:** permutations, combinations, subsets, path enumeration.

### Template
```python
def backtrack(path, choices):
    if goal_reached:
        result.append(path.copy())
        return

    for choice in choices:
        if not allowed(choice):
            continue

        # Choose
        path.append(choice)

        # Explore
        backtrack(path, updated_choices)

        # Undo
        path.pop()
```

Example: generate all subsets.
```python
def subsets(nums):
    result = []
    path = []

    def backtrack(start):
        result.append(path.copy())

        for i in range(start, len(nums)):
            path.append(nums[i])
            backtrack(i + 1)
            path.pop()

    backtrack(0)
    return result
```

> **Pattern:** Choose, explore, undo.

> `path.copy()` matters because otherwise the results may all refer to the same mutable list.

---

## 16. Dynamic Programming (DP)

**Use for:** repeated subproblems, optimisation, counting ways, decisions over time.

### Pattern A: one-dimensional DP
Example: climbing stairs.
```python
def climbStairs(n):
    if n <= 2:
        return n

    dp = [0] * (n + 1)
    dp[1] = 1
    dp[2] = 2

    for i in range(3, n + 1):
        dp[i] = dp[i - 1] + dp[i - 2]

    return dp[n]
```

### Pattern B: memoized recursion
```python
from functools import lru_cache

def solve(n):
    @lru_cache(None)
    def dp(i):
        if i == 0:
            return base_answer

        return combine(dp(smaller_state))

    return dp(n)
```

> The state and base case must be designed for the actual problem.

### Pattern C: two-dimensional DP
```python
dp = [[0] * cols for _ in range(rows)]

# Initialize base cases

for r in range(rows):
    for c in range(cols):
        # Compute dp[r][c] from smaller states
        ...
```

> **Pattern:** Define what `dp[i]` or `dp[i][j]` means before writing the recurrence.

---

## 17. Intervals

**Use for:** merging intervals, scheduling, overlaps.

### Template: merge intervals
```python
def merge(intervals):
    intervals.sort()
    result = []

    for start, end in intervals:
        if not result or start > result[-1][1]:
            result.append([start, end])
        else:
            result[-1][1] = max(result[-1][1], end)

    return result
```

> **Pattern:** Sort by start time, then compare each interval with the most recently merged interval.

> This template treats intervals that touch at an endpoint as overlapping.

---

## 18. Topological Sort

**Use for:** course schedules, prerequisites, dependency ordering in directed acyclic graphs.

### Kahn's algorithm
```python
from collections import deque

def topologicalSort(n, edges):
    graph = [[] for _ in range(n)]
    indegree = [0] * n

    for u, v in edges:
        graph[u].append(v)
        indegree[v] += 1

    q = deque(
        node for node in range(n)
        if indegree[node] == 0
    )

    order = []

    while q:
        node = q.popleft()
        order.append(node)

        for nei in graph[node]:
            indegree[nei] -= 1

            if indegree[nei] == 0:
                q.append(nei)

    if len(order) != n:
        return []  # Cycle exists

    return order
```

> Here, an edge `u -> v` means `u` must come before `v`.

> **Pattern:** Process nodes with no remaining prerequisites. If not all nodes are processed, a cycle exists.

---

## 19. Union-Find / Disjoint Set Union (DSU)

**Use for:** dynamic connectivity, grouping connected components, cycle detection in undirected graphs.

```python
class DSU:
    def __init__(self, n):
        self.parent = list(range(n))
        self.size = [1] * n

    def find(self, x):
        if self.parent[x] != x:
            self.parent[x] = self.find(self.parent[x])
        return self.parent[x]

    def union(self, a, b):
        ra = self.find(a)
        rb = self.find(b)

        if ra == rb:
            return False

        if self.size[ra] < self.size[rb]:
            ra, rb = rb, ra

        self.parent[rb] = ra
        self.size[ra] += self.size[rb]

        return True
```

> **Pattern:** `find` identifies a component's representative; `union` merges two components.

> With path compression and union by size, operations are effectively constant time for typical problem constraints.

---

## 20. Trie / Prefix Tree

**Use for:** prefix searches, word dictionaries, autocomplete.

```python
class TrieNode:
    def __init__(self):
        self.children = {}
        self.is_word = False

class Trie:
    def __init__(self):
        self.root = TrieNode()

    def insert(self, word):
        node = self.root

        for ch in word:
            if ch not in node.children:
                node.children[ch] = TrieNode()

            node = node.children[ch]

        node.is_word = True

    def search(self, word):
        node = self._find(word)
        return node is not None and node.is_word

    def startsWith(self, prefix):
        return self._find(prefix) is not None

    def _find(self, text):
        node = self.root

        for ch in text:
            if ch not in node.children:
                return None
            node = node.children[ch]

        return node
```

> **Pattern:** Each character corresponds to an edge. `is_word` distinguishes a complete stored word from a prefix.

---

# PART 3 — HOW TO CHOOSE A PATTERN

| Problem clue | Consider |
|---|---|
| Fast lookup or frequency | Hash map / set |
| Sorted array, pair sum | Two pointers / binary search |
| Contiguous subarray or substring | Sliding window / prefix sums |
| Shortest path in an unweighted graph | BFS |
| Explore connected regions | DFS / BFS |
| Tree height or subtree properties | Recursive DFS |
| Tree level-by-level | BFS with a queue |
| Repeated choices and undoing | Backtracking |
| Repeated subproblems | Dynamic programming |
| Top K or repeatedly take minimum | Heap |
| Reverse or rearrange linked nodes | Pointer manipulation |
| Overlapping time ranges | Sorting and intervals |
| Prerequisites and dependencies | Topological sort |
| Connectivity across many edges | Union-Find |
| Prefix and word lookup | Trie |
| Next greater/smaller element | Monotonic stack |

> These are starting points, not rigid rules. Some problems combine multiple patterns.

---

# PART 4 — A REUSABLE PROBLEM-SOLVING CHECKLIST

Before coding, answer these questions:

1. **Input and output:** What am I given, and what must I return?
2. **Data structure:** What operations do I need?
3. **Pattern:** What kind of problem is this?
4. **State:** What variables do I need to keep track of?
5. **Base case:** If using recursion, what is the smallest subproblem?
6. **Progress:** What changes each iteration or recursive call?
7. **Correctness:** Why won't I skip a valid answer or process something twice?
8. **Complexity:** How much time and space will the solution use?

## Your practice plan

Learn these in roughly this order:

1. Lists, sets, dictionaries and strings
2. Two pointers and sliding windows
3. Stacks, queues and binary search
4. Linked lists
5. Trees: DFS, BFS and recursion
6. Graphs and grid traversal
7. Heaps and intervals
8. Backtracking
9. Dynamic programming
10. Topological sort, Union-Find and tries

Don't try
