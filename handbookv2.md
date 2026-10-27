LeetCode Data Structures Cheat Sheet (Python)
1. Binary Trees
python
# Access
root.val
root.left
root.right

# Check empty
if not root:
    return ...

# Create a node
root = TreeNode(1)
root.left = TreeNode(2)
root.right = TreeNode(3)

# Recursive DFS
def dfs(root):
    if not root:
        return ...

    left = dfs(root.left)
    right = dfs(root.right)
    return ...


Trees use .left and .right to connect nodes, not .append().

2. Arrays / Lists
python
arr = [1, 2, 3]

arr[i]          # Access
arr.append(x)   # Add to end
arr.pop()       # Remove and return last
len(arr)        # Length
arr.sort()      # Sort in place
arr.reverse()   # Reverse in place
x in arr        # Membership
arr[1:3]        # Slice

3. Hash Sets
python
seen = set()

seen.add(x)         # Add
seen.remove(x)      # Remove; errors if absent
seen.discard(x)     # Remove safely
x in seen           # Membership
x not in seen       # Absence
len(seen)           # Number of elements

4. Dictionaries / Hash Maps
python
counts = {}

counts[key] = value
counts.get(key, 0)
key in counts
del counts[key]
counts.items()

# Frequency counter
for x in nums:
    counts[x] = counts.get(x, 0) + 1

5. Stacks (LIFO)
python
stack = []

stack.append(x)  # Push
stack.pop()      # Pop top
stack[-1]        # Peek at top
if not stack:    # Check empty
    ...


Common uses: DFS, parentheses, monotonic stacks.

6. Queues / Deques (FIFO)
python
from collections import deque

q = deque()
q.append(x)       # Add to back
q.popleft()       # Remove from front
q[0]              # Peek at front

if not q:
    ...


Common uses: BFS, level-order tree traversal, grid traversal.

7. Linked Lists
python
# Access
node.val
node.next

# Check empty
if not node:
    return ...

# Create and connect nodes
node = ListNode(5)
node.next = other


Unlike arrays, linked lists generally aren't accessed using node[i].

8. Heaps / Priority Queues
python
import heapq

heap = []

heapq.heappush(heap, x)  # Insert
heapq.heappop(heap)      # Remove smallest
heap[0]                  # Peek at smallest
len(heap)                # Size


Python's heapq is a min-heap by default.

Common Imports
python
from collections import deque, defaultdict, Counter
import heapq

Tree Recursion Checklist
What does my function return?
What should it return when root is None?
What smaller subproblems do I need to solve?
How do I combine their results?
Does every recursive call move toward a base case?
Base Case Examples
Problem	Empty tree result
Maximum depth (measured in nodes)	0
Count nodes	0
Sum nodes	0
Is balanced?	True
Search for a value	None or False
Height (measured in edges)	-1

Remember: The base case depends on what your function is calculating.

BFS Template
python
from collections import deque

def bfs(root):
    if not root:
        return

    q = deque([root])

    while q:
        node = q.popleft()

        if node.left:
            q.append(node.left)

        if node.right:
            q.append(node.right)

DFS Template
python
def dfs(root):
    if not root:
        return

    # Process current node
    print(root.val)

    dfs(root.left)
    dfs(root.right)


Keep this sheet open while practising. Looking up syntax is fine; repeated use will make it automatic.
