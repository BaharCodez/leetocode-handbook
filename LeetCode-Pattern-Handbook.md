# 🧭 LeetCode Pattern Recognition Handbook

A personal field guide for SWE internship interviews. Goal: **see a new problem → recognize the pattern → know the technique.** Not a problem dump.

**How to read this:** Each topic is broken into a few recurring patterns. For each pattern you get: the question to ask yourself, a mental model, a memorizable Python template, recognition clues, representative problems, complexity, and a tiny worked example. Every topic ends with a 🧠 Recognition Checklist and 🔥 Must-Know Problems.

**Priority tags:**
- 🟢 **Must know** for internships — will show up constantly.
- 🟡 **Good to know** — appears sometimes, worth understanding.
- 🔴 **Advanced / lower priority** — only if you have time.

---

# 1. Arrays & Hashing 🟢

The workhorse. Hashmaps/sets turn "search the array again" (O(n)) into "look it up" (O(1)).

## Pattern 1 — "Have I seen this before?"

**Ask yourself:** *"Do I just need to know if something already appeared?"*

**Plain language:** Walk through the array once, remembering what you've seen in a `set`. Before processing an item, check if it (or its "partner") is already there.

**Mental model:** A bouncer with a guest list. For each person, check the list; if they're on it, act; otherwise add them.

**Code template**
```python
def has_seen(nums):
    seen = set()
    for x in nums:
        if x in seen:        # seen before?
            return True
        seen.add(x)
    return False
```

**When to use it:** clues = "contains duplicate", "does a pair exist", "is it unique", "complement" (like Two Sum: check if `target - x` was seen).

**Common problems:** Contains Duplicate (E), Two Sum (E), Longest Consecutive Sequence (M).

**Complexity:** Time O(n), Space O(n).

**Example** — Two Sum, `nums=[2,7,11]`, `target=9`:
- x=2: need 7, not seen → store {2}.
- x=7: need 2, **2 is seen** → answer.

**Doesn't work / mistakes:** if you need the actual *index or value later*, a bare set isn't enough — use a dict (Pattern 3). Forgetting to add `x` after checking.

## Pattern 2 — "How many times have I seen this?"

**Ask yourself:** *"Do I care about counts / frequencies?"*

**Plain language:** Count occurrences of each item in a dict.

**Mental model:** Tally marks on a whiteboard, one row per distinct value.

**Code template**
```python
from collections import Counter
count = Counter(nums)          # {value: frequency}
# or manually:
count = {}
for x in nums:
    count[x] = count.get(x, 0) + 1
```

**When to use it:** "anagram", "frequency", "most common", "appears k times", "can we rearrange".

**Common problems:** Valid Anagram (E), Majority Element (E), Top K Frequent Elements (M), Group Anagrams (M).

**Complexity:** Time O(n), Space O(n).

**Example** — Valid Anagram `"eat"` vs `"tea"`: both count to `{e:1,a:1,t:1}` → equal → true.

**Mistakes:** comparing counts by sorting when a Counter is O(n) vs O(n log n); forgetting different string lengths can't be anagrams.

## Pattern 3 — "Where did I see this?"

**Ask yourself:** *"Do I need the index/position of something I saw earlier?"*

**Plain language:** Store `value → index` (or value → last position) so you can look it back up.

**Code template**
```python
seen = {}                      # value -> index
for i, x in enumerate(nums):
    if target - x in seen:
        return [seen[target - x], i]
    seen[x] = i
```

**When to use it:** "return the indices", "distance between duplicates", "first/last position".

**Common problems:** Two Sum (E, index version), Contains Duplicate II (E), Longest Substring Without Repeating (M).

**Complexity:** Time O(n), Space O(n).

**Mistakes:** overwriting an index you still needed; storing before checking (can match itself).

🧠 **Recognition Checklist — Arrays & Hashing**
- If I see "duplicate / exists / unique" → think **set: have I seen it?**
- If I see "frequency / anagram / count / top-k" → think **Counter**.
- If I need the **index later** → think **dict value→index**.
- If I need "complement/partner sums to target" → think **set/dict + `target - x`**.
- If the array is sorted → maybe I don't need a hashmap at all (Two Pointers).

🔥 **Must-Know Problems**
1. Contains Duplicate (E)
2. Two Sum (E)
3. Valid Anagram (E)
4. Group Anagrams (M)
5. Top K Frequent Elements (M)
6. Product of Array Except Self (M)
7. Longest Consecutive Sequence (M)

---

# 2. Two Pointers 🟢

Two indices moving through data, usually to avoid a nested loop. Most powerful on **sorted** arrays.

## Pattern 1 — "Converging pointers" (left & right ends)

**Ask yourself:** *"Is the array sorted, and am I looking for a pair/triple with some sum condition?"*

**Plain language:** Start one pointer at each end. If the combo is too big, move `right` left; too small, move `left` right.

**Mental model:** Tuning a value with two dials — squeeze inward until it's just right.

**Code template**
```python
def two_sum_sorted(nums, target):
    l, r = 0, len(nums) - 1
    while l < r:
        s = nums[l] + nums[r]
        if s == target: return [l, r]
        if s < target:  l += 1     # need bigger → move left up
        else:           r -= 1     # need smaller → move right down
    return []
```

**When to use it:** "sorted array", "pair sums to X", "3Sum", "container / max area", "is palindrome".

**Common problems:** Valid Palindrome (E), Two Sum II sorted (E→M), 3Sum (M), Container With Most Water (M).

**Complexity:** Time O(n) (or O(n²) for 3Sum), Space O(1).

**Example** — `[1,2,4,7]`, target 6: l=1,r=7 → 8>6, r→4; 1+4=5<6, l→2; 2+4=6 ✓.

**Doesn't work:** array not sorted (sort first if order doesn't matter). **Mistakes:** forgetting to skip duplicates in 3Sum.

## Pattern 2 — "Fast & slow / same-direction pointers"

**Ask yourself:** *"Am I building a filtered/compacted result in place, or removing things?"*

**Plain language:** A `slow` pointer marks where the next kept element goes; a `fast` pointer scans ahead.

**Code template**
```python
def remove_dups(nums):           # sorted, in-place
    slow = 0
    for fast in range(len(nums)):
        if fast == 0 or nums[fast] != nums[slow]:
            nums[slow] = nums[fast]
            slow += 1
    return slow                  # new length
```

**When to use it:** "remove in place", "move zeroes", "in-place O(1) space", cycle detection in linked lists.

**Common problems:** Move Zeroes (E), Remove Duplicates from Sorted Array (E), Remove Element (E).

**Complexity:** Time O(n), Space O(1).

**Mistakes:** confusing what `slow` points to (next-write vs last-written).

🧠 **Recognition Checklist — Two Pointers**
- If array is **sorted** + looking for a pair/sum → **converging pointers**.
- If "in place / O(1) space / remove / compact" → **slow & fast, same direction**.
- If "palindrome" → **converging from both ends**.
- If "3Sum/4Sum" → **sort, fix one, two-pointer the rest**.

🔥 **Must-Know Problems**
1. Valid Palindrome (E)
2. Move Zeroes (E)
3. Two Sum II — Sorted (M)
4. 3Sum (M)
5. Container With Most Water (M)
6. Trapping Rain Water (H, stretch)

---

# 3. Sliding Window 🟢

For **contiguous** subarrays/substrings. Turns O(n²) "check every window" into O(n) by reusing work as the window slides.

Core mantra: **"Expand with `right`. Shrink with `left`."**

## Pattern 1 — Fixed-size window

**Ask yourself:** *"Is the window a fixed length k?"*

**Plain language:** Slide a window of exactly `k`. Add the entering element, remove the leaving one.

**Code template**
```python
def max_sum_k(nums, k):
    window = sum(nums[:k])
    best = window
    for right in range(k, len(nums)):
        window += nums[right] - nums[right - k]  # add new, drop old
        best = max(best, window)
    return best
```

**When to use it:** "subarray of size k", "average of k", "max/min sum of k consecutive".

**Common problems:** Maximum Average Subarray I (E), Max Sum of k (E).

**Complexity:** Time O(n), Space O(1).

## Pattern 2 — Variable-size window (expand then shrink)

**Ask yourself:** *"Do I want the longest/shortest window satisfying a condition?"*

**Plain language:** Grow `right` to include more. When the window breaks a rule, shrink from `left` until it's valid again.

**Mental model:** An elastic band: stretch to the right; when it's too loose/tight, pull the left in.

**Code template**
```python
def longest_valid(s):
    left = 0
    best = 0
    state = {}                      # window contents (hashmap/counter)
    for right, ch in enumerate(s):
        state[ch] = state.get(ch, 0) + 1
        while not valid(state):     # rule broken → shrink
            state[s[left]] -= 1
            if state[s[left]] == 0: del state[s[left]]
            left += 1
        best = max(best, right - left + 1)
    return best
```

**When to use it:** "longest substring with…", "shortest subarray ≥ target", "at most k distinct", "no repeating chars".

**Common problems:** Longest Substring Without Repeating Chars (M), Longest Repeating Character Replacement (M), Minimum Size Subarray Sum (M), Minimum Window Substring (H).

**Complexity:** Time O(n) (each element enters/leaves once), Space O(k).

**Example** — Longest no-repeat in `"abca"`: expand a,b,c (window "abc"); `right`=a repeats → shrink left past first a → window "bca", length 3.

**Doesn't work:** non-contiguous problems (subsequences ≠ subarrays). **Mistakes:** shrinking with `if` instead of `while`; updating `best` at the wrong time.

**If I need to remember what's inside the window → use a hashmap / frequency counter** (Pattern 2's `state`). "Longest" usually shrinks only when invalid; "shortest" shrinks *while still valid* to minimize.

🧠 **Recognition Checklist — Sliding Window**
- If "contiguous subarray/substring" → **think sliding window** (not subsequence).
- If window size is given (k) → **fixed window**.
- If "longest/shortest satisfying X" → **variable window, expand+shrink**.
- If I must track contents/counts → **window + hashmap/counter**.
- "Longest" → shrink when **invalid**. "Shortest" → shrink while **valid**.

🔥 **Must-Know Problems**
1. Maximum Average Subarray I (E)
2. Best Time to Buy/Sell Stock (E)
3. Longest Substring Without Repeating Characters (M)
4. Longest Repeating Character Replacement (M)
5. Minimum Size Subarray Sum (M)
6. Permutation in String (M)
7. Minimum Window Substring (H, stretch)

---

# 4. Stack 🟢

LIFO. Great when the **most recent** unfinished thing is what you need next.

## Pattern 1 — Matching / balanced pairs

**Ask yourself:** *"Do I need to match opening things with the most recent opening?"*

**Plain language:** Push openers; when you hit a closer, it must match the top of the stack.

**Code template**
```python
def valid_parens(s):
    pairs = {')': '(', ']': '[', '}': '{'}
    stack = []
    for c in s:
        if c in pairs:                       # closer
            if not stack or stack.pop() != pairs[c]:
                return False
        else:
            stack.append(c)                  # opener
    return not stack
```

**When to use it:** "valid parentheses", "matching", "undo", "nested".

**Common problems:** Valid Parentheses (E), Min Stack (M), Evaluate Reverse Polish Notation (M), Generate Parentheses (M, +backtracking).

**Complexity:** Time O(n), Space O(n).

**Mistakes:** forgetting the "stack empty at closer" case; not checking the stack is empty at the end.

## Pattern 2 — "Process later / defer with a stack"

**Ask yourself:** *"Do I need to pair each item with a future/past element based on order?"* → often a **Monotonic Stack** (see topic 22).

🧠 **Recognition Checklist — Stack**
- If "parentheses / brackets / matching / nested" → **stack**.
- If "most recent unresolved item matters" → **stack**.
- If "next greater/smaller element" → **monotonic stack** (topic 22).
- If "evaluate expression / RPN" → **stack**.

🔥 **Must-Know Problems**
1. Valid Parentheses (E)
2. Min Stack (M)
3. Evaluate Reverse Polish Notation (M)
4. Daily Temperatures (M, monotonic)
5. Car Fleet (M)

---

# 5. Binary Search 🟢

Halve the search space each step. Needs a **sorted / monotonic** structure. O(log n).

## Pattern 1 — Classic search on a sorted array

**Ask yourself:** *"Is the data sorted and I'm looking for a value (or where it'd go)?"*

**Code template**
```python
def binary_search(nums, target):
    lo, hi = 0, len(nums) - 1
    while lo <= hi:
        mid = (lo + hi) // 2
        if nums[mid] == target: return mid
        if nums[mid] < target:  lo = mid + 1
        else:                   hi = mid - 1
    return -1
```

**When to use it:** "sorted array", "find target", "O(log n) required".

**Common problems:** Binary Search (E), Search Insert Position (E), Search a 2D Matrix (M).

**Complexity:** Time O(log n), Space O(1).

## Pattern 2 — "Binary search on the answer"

**Ask yourself:** *"Can I guess an answer and check 'is it feasible?' — and does feasibility only flip once (monotonic)?"*

**Plain language:** The answer lives in a numeric range. Binary search the *answer*, using a feasibility check.

**Mental model:** "What's the smallest speed that lets me finish in time?" Guess a speed, test yes/no, narrow.

**Code template**
```python
def min_feasible(lo, hi, feasible):
    while lo < hi:
        mid = (lo + hi) // 2
        if feasible(mid): hi = mid       # mid works → try smaller
        else:             lo = mid + 1   # too small → go bigger
    return lo
```

**When to use it:** "minimum/maximum X such that…", "Koko eating bananas", "capacity to ship", "split array".

**Common problems:** Koko Eating Bananas (M), Capacity to Ship Packages (M), Find Min in Rotated Sorted Array (M), Median of Two Sorted Arrays (H).

**Complexity:** Time O(n log(range)), Space O(1).

**Mistakes:** `(lo+hi)//2` off-by-one causing infinite loops — pick `lo<=hi` (return index) vs `lo<hi` (converge to boundary) deliberately and stick with it.

🧠 **Recognition Checklist — Binary Search**
- If "sorted" + "find" → **classic binary search**.
- If "min/max value such that a check passes" → **binary search on the answer**.
- If "rotated sorted array" → **modified binary search** (find the sorted half).
- If required complexity is **O(log n)** → binary search is the hint.

🔥 **Must-Know Problems**
1. Binary Search (E)
2. Search Insert Position (E)
3. Find Minimum in Rotated Sorted Array (M)
4. Search in Rotated Sorted Array (M)
5. Koko Eating Bananas (M)
6. Median of Two Sorted Arrays (H, stretch)

---

# 6. Linked Lists 🟢

Nodes pointing to nodes. Almost everything is a variation of **pointer manipulation**. Trick: use a **dummy head** to simplify edge cases.

## Pattern 1 — Two pointers (fast & slow)

**Ask yourself:** *"Do I need the middle, a cycle, or the k-th from the end — without knowing the length?"*

**Mental model:** Two runners on a track; the fast one laps or reaches the end first, revealing structure.

**Code template**
```python
def middle(head):
    slow = fast = head
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
    return slow                  # middle (or cycle meeting point)
```

**When to use it:** "find middle", "detect cycle", "nth from end", "is palindrome list".

**Common problems:** Middle of Linked List (E), Linked List Cycle (E), Remove Nth Node From End (M).

**Complexity:** Time O(n), Space O(1).

## Pattern 2 — Reverse a list (or part of it)

**Ask yourself:** *"Do I need to reverse direction of pointers?"*

**Code template**
```python
def reverse(head):
    prev = None
    cur = head
    while cur:
        nxt = cur.next           # save next
        cur.next = prev          # flip pointer
        prev = cur               # advance
        cur = nxt
    return prev                  # new head
```

**When to use it:** "reverse list", "reverse in groups", "reorder", palindrome check.

**Common problems:** Reverse Linked List (E), Reverse Linked List II (M), Reorder List (M).

**Complexity:** Time O(n), Space O(1).

## Pattern 3 — Merge / dummy head

**Ask yourself:** *"Am I building a new list by stitching nodes?"*

**Code template**
```python
def merge(a, b):
    dummy = tail = ListNode()
    while a and b:
        if a.val <= b.val: tail.next, a = a, a.next
        else:              tail.next, b = b, b.next
        tail = tail.next
    tail.next = a or b
    return dummy.next
```

**When to use it:** "merge two lists", "merge k lists" (+heap), "add two numbers".

**Common problems:** Merge Two Sorted Lists (E), Add Two Numbers (M), Merge k Sorted Lists (H, +heap).

**Complexity:** Time O(n), Space O(1).

**Mistakes:** losing the `next` pointer before reassigning; forgetting the dummy; not attaching the leftover tail.

🧠 **Recognition Checklist — Linked Lists**
- If "middle / cycle / nth-from-end" → **fast & slow**.
- If "reverse / reorder" → **reverse pointers with prev/cur/nxt**.
- If "merge / build new list" → **dummy head + tail**.
- Edge cases everywhere → **use a dummy node**.

🔥 **Must-Know Problems**
1. Reverse Linked List (E)
2. Merge Two Sorted Lists (E)
3. Linked List Cycle (E)
4. Remove Nth Node From End (M)
5. Reorder List (M)
6. Add Two Numbers (M)
7. Merge k Sorted Lists (H, stretch)

---

# 7. Trees 🟢

Recursion's home turf. **"Solve for a node assuming its children are already solved."**

## Pattern 1 — DFS recursion (the default)

**Ask yourself:** *"Can I express the answer for a node in terms of its left/right subtree answers?"*

**Mental model:** Trust the recursion: pretend a helper already gives you the correct left and right results; just combine.

**Code template**
```python
def dfs(node):
    if not node: return 0        # base case
    left = dfs(node.left)
    right = dfs(node.right)
    return 1 + max(left, right)  # combine (e.g. height)
```

**When to use it:** "depth/height", "sum", "path", "compare trees", "is balanced".

**Common problems:** Max Depth (E), Same Tree (E), Invert Tree (E), Diameter (M), Balanced Tree (E→M).

**Complexity:** Time O(n), Space O(h) recursion (h = height).

## Pattern 2 — BFS / level order (queue)

**Ask yourself:** *"Do I care about levels, or the shortest depth?"*

**Code template**
```python
from collections import deque
def level_order(root):
    if not root: return []
    q, out = deque([root]), []
    while q:
        level = []
        for _ in range(len(q)):   # fix the level size
            n = q.popleft()
            level.append(n.val)
            if n.left:  q.append(n.left)
            if n.right: q.append(n.right)
        out.append(level)
    return out
```

**When to use it:** "level order", "right side view", "min depth", "zigzag".

**Common problems:** Level Order Traversal (M), Right Side View (M), Min Depth (E).

**Complexity:** Time O(n), Space O(n).

**Mistakes:** not snapshotting `len(q)` before the inner loop; recursion depth on skewed trees.

🧠 **Recognition Checklist — Trees**
- If "combine children's answers" → **DFS recursion**.
- If "levels / shortest depth / row-by-row" → **BFS with a queue**.
- If "path sum / max path" → **DFS returning a value + a global best**.
- If binary **search** tree → exploit ordering (topic 8).

🔥 **Must-Know Problems**
1. Maximum Depth of Binary Tree (E)
2. Invert Binary Tree (E)
3. Same Tree (E)
4. Diameter of Binary Tree (M)
5. Binary Tree Level Order Traversal (M)
6. Lowest Common Ancestor (M)
7. Binary Tree Max Path Sum (H, stretch)

---

# 8. Binary Search Trees 🟡

A BST keeps **left < node < right**. That ordering lets you prune half the tree.

## Pattern 1 — Ordered navigation

**Ask yourself:** *"Can I go left or right based on comparison, skipping half?"*

**Code template**
```python
def search_bst(root, target):
    while root:
        if target == root.val: return root
        root = root.left if target < root.val else root.right
    return None
```

**When to use it:** "search/insert/delete in BST", "LCA in BST", "validate BST", "kth smallest".

**Mental model:** A guessing game where each node tells you "go smaller" or "go bigger".

**Common problems:** Search in a BST (E), Validate BST (M), Kth Smallest in BST (M), LCA of BST (M).

**Complexity:** Time O(h) — O(log n) balanced, O(n) worst.

## Pattern 2 — Inorder traversal = sorted order

**Ask yourself:** *"Do I need BST values in sorted order (kth smallest, validation)?"*

**Code template**
```python
def inorder(node, out):
    if not node: return
    inorder(node.left, out)
    out.append(node.val)         # visited in sorted order
    inorder(node.right, out)
```

**When to use it:** "kth smallest/largest", "validate BST", "range sum".

**Complexity:** Time O(n), Space O(h).

**Mistakes:** validating a BST by only comparing parent–child (must carry down `low/high` bounds).

🧠 **Recognition Checklist — BST**
- If "BST" + "search/insert" → **ordered navigation (go left/right)**.
- If "kth smallest / sorted / validate" → **inorder traversal**.
- If "LCA in a BST" → **walk down comparing to both targets**.

🔥 **Must-Know Problems**
1. Search in a BST (E)
2. Lowest Common Ancestor of a BST (M)
3. Validate Binary Search Tree (M)
4. Kth Smallest Element in a BST (M)

---

# 9. Heap / Priority Queue 🟢

A heap always gives you the **smallest (or largest)** item in O(log n). Python's `heapq` is a **min-heap**.

## Pattern 1 — "Top K"

**Ask yourself:** *"Do I need the k largest/smallest/most-frequent — but not full sorting?"*

**Plain language:** Keep a heap of size k. It's cheaper than sorting everything (O(n log k) vs O(n log n)).

**Mental model:** A leaderboard that only keeps the top k; the weakest gets bumped.

**Code template**
```python
import heapq
def top_k_largest(nums, k):
    heap = []
    for x in nums:
        heapq.heappush(heap, x)
        if len(heap) > k:
            heapq.heappop(heap)   # drop smallest → keep k largest
    return heap
```

**When to use it:** "top k", "k closest", "kth largest", "most frequent".

**Common problems:** Kth Largest Element (M), Top K Frequent (M), K Closest Points (M).

**Complexity:** Time O(n log k), Space O(k).

## Pattern 2 — "Always grab the next smallest/largest" (merge / scheduling)

**Ask yourself:** *"Do I repeatedly need the current min/max as things change?"*

**Code template**
```python
import heapq
def merge_k(lists):
    heap = [(l.val, i, l) for i, l in enumerate(lists) if l]
    heapq.heapify(heap)
    dummy = tail = ListNode()
    while heap:
        val, i, node = heapq.heappop(heap)
        tail.next = node; tail = node
        if node.next:
            heapq.heappush(heap, (node.next.val, i, node.next))
    return dummy.next
```

**When to use it:** "merge k lists", "task scheduler", "meeting rooms count", "median from stream" (two heaps).

**Common problems:** Merge k Sorted Lists (H), Task Scheduler (M), Find Median from Data Stream (H).

**Complexity:** Time O(n log k), Space O(k).

**Mistakes:** forgetting Python is a min-heap (push negatives for a max-heap); unorderable tuples when vals tie (add a tiebreaker index).

🧠 **Recognition Checklist — Heap**
- If "top k / kth largest / k closest" → **size-k heap**.
- If "repeatedly take min/max" → **heap as a live priority queue**.
- If "median of a stream" → **two heaps** (max-heap low half, min-heap high half).
- Max-heap in Python → **push `-value`**.

🔥 **Must-Know Problems**
1. Kth Largest Element in an Array (M)
2. Top K Frequent Elements (M)
3. K Closest Points to Origin (M)
4. Task Scheduler (M)
5. Find Median from Data Stream (H, stretch)

---

# 10. Intervals 🟡

Problems about ranges `[start, end]`. The universal first move: **sort by start** (sometimes by end).

## Pattern 1 — Merge overlapping intervals

**Ask yourself:** *"Do overlapping ranges need to be combined?"*

**Mental model:** Painting over a timeline — overlapping strokes fuse into one.

**Code template**
```python
def merge(intervals):
    intervals.sort()
    out = [intervals[0]]
    for s, e in intervals[1:]:
        if s <= out[-1][1]:                 # overlaps last
            out[-1][1] = max(out[-1][1], e)  # extend
        else:
            out.append([s, e])
    return out
```

**When to use it:** "merge intervals", "insert interval", "overlapping".

**Common problems:** Merge Intervals (M), Insert Interval (M).

**Complexity:** Time O(n log n), Space O(n).

## Pattern 2 — Count / remove for non-overlap (greedy by end)

**Ask yourself:** *"How many can I keep/remove so nothing overlaps? Do I need a room count?"*

**Code template**
```python
def min_rooms(intervals):
    starts = sorted(i[0] for i in intervals)
    ends   = sorted(i[1] for i in intervals)
    rooms = maxr = 0; e = 0
    for s in starts:
        if s < ends[e]: rooms += 1          # new overlap → need a room
        else:           e += 1              # one meeting freed a room
        maxr = max(maxr, rooms)
    return maxr
```

**When to use it:** "meeting rooms", "non-overlapping intervals", "erase overlap".

**Common problems:** Non-overlapping Intervals (M), Meeting Rooms (E), Meeting Rooms II (M).

**Complexity:** Time O(n log n), Space O(n).

**Mistakes:** forgetting to sort; wrong tie-break (`<=` vs `<`) at touching endpoints.

🧠 **Recognition Checklist — Intervals**
- Almost always → **sort by start first**.
- If "combine overlaps" → **merge pattern**.
- If "how many rooms / max concurrent" → **sweep starts vs ends**.
- If "remove fewest to avoid overlap" → **greedy, sort by end**.

🔥 **Must-Know Problems**
1. Meeting Rooms (E)
2. Merge Intervals (M)
3. Insert Interval (M)
4. Non-overlapping Intervals (M)
5. Meeting Rooms II (M)

---

# 11. Greedy 🟡

Make the **locally best choice** at each step and never look back. Works only when local optimum ⇒ global optimum.

## Pattern 1 — "Best choice right now"

**Ask yourself:** *"Can I prove that grabbing the best option now can't hurt later?"*

**Mental model:** Packing a bag by always taking the most valuable item that still fits — when that genuinely works.

**Code template**
```python
def jump(nums):                  # can I reach the end?
    reach = 0
    for i, n in enumerate(nums):
        if i > reach: return False
        reach = max(reach, i + n)
    return True
```

**When to use it:** "maximum/minimum", "can you reach", "fewest steps", "intervals", "assign".

**Common problems:** Best Time to Buy/Sell Stock (E), Jump Game (M), Gas Station (M), Partition Labels (M).

**Complexity:** usually Time O(n) or O(n log n), Space O(1).

**Doesn't work / mistakes:** many problems *look* greedy but need DP (e.g. coin change with odd denominations). If a counterexample breaks your greedy choice → switch to DP. Always sanity-check greedy on a tricky case.

🧠 **Recognition Checklist — Greedy**
- If "max/min" + a natural "take the best now" step → **try greedy**, then verify.
- If greedy has a counterexample → **fall back to DP**.
- If "intervals / scheduling" → greedy **sorted by end** is common.

🔥 **Must-Know Problems**
1. Best Time to Buy and Sell Stock (E)
2. Maximum Subarray (M — Kadane)
3. Jump Game (M)
4. Gas Station (M)
5. Partition Labels (M)

---

# 12. Backtracking 🟢

Build a solution incrementally; when a partial choice can't work, **undo it and try another**. It's DFS over "choices".

Core shape: **choose → explore → un-choose.**

## Pattern 1 — Subsets / combinations / permutations

**Ask yourself:** *"Do I need to generate all combinations/arrangements/subsets?"*

**Mental model:** Exploring a maze, dropping breadcrumbs; hit a dead end → walk back and pick a different turn.

**Code template**
```python
def subsets(nums):
    res, path = [], []
    def dfs(start):
        res.append(path[:])           # record current subset
        for i in range(start, len(nums)):
            path.append(nums[i])      # choose
            dfs(i + 1)                # explore
            path.pop()                # un-choose
    dfs(0)
    return res
```

**When to use it:** "all subsets/permutations/combinations", "generate", "N-Queens", "word search", "palindrome partitioning".

**Common problems:** Subsets (M), Combination Sum (M), Permutations (M), Word Search (M), N-Queens (H).

**Complexity:** exponential — O(2ⁿ), O(n!) etc. Space O(n) for recursion + path.

**When it doesn't work / mistakes:** if only a *count* or *best value* is needed (not every solution) it's often DP instead. Forgetting to `pop()` (the un-choose) corrupts state. Add **pruning** (skip branches that can't succeed) to survive time limits; skip duplicates by sorting + `if i>start and nums[i]==nums[i-1]: continue`.

🧠 **Recognition Checklist — Backtracking**
- If "generate all ___" → **backtracking**.
- If "combinations/permutations/subsets" → **choose/explore/un-choose**.
- If "grid word search / N-Queens / sudoku" → **backtracking with pruning**.
- If you only need **count or optimum** → consider **DP** instead.

🔥 **Must-Know Problems**
1. Subsets (M)
2. Combination Sum (M)
3. Permutations (M)
4. Word Search (M)
5. Palindrome Partitioning (M)
6. N-Queens (H, stretch)

---

# 13. Graphs & BFS/DFS 🟢

Graphs = nodes + edges. Grids are graphs in disguise (each cell connects to neighbors). Two core traversals: **DFS** (go deep) and **BFS** (go level by level → shortest path in unweighted graphs).

**Always carry a `visited` set** to avoid infinite loops.

## Pattern 1 — DFS (flood fill / connected components)

**Ask yourself:** *"Do I need to explore an entire region / count components?"*

**Code template**
```python
def num_islands(grid):
    rows, cols = len(grid), len(grid[0])
    seen = set()
    def dfs(r, c):
        if (r < 0 or c < 0 or r >= rows or c >= cols
                or grid[r][c] == '0' or (r, c) in seen):
            return
        seen.add((r, c))
        for dr, dc in ((1,0),(-1,0),(0,1),(0,-1)):
            dfs(r + dr, c + dc)
    count = 0
    for r in range(rows):
        for c in range(cols):
            if grid[r][c] == '1' and (r, c) not in seen:
                dfs(r, c); count += 1
    return count
```

**When to use it:** "number of islands", "connected components", "flood fill", "can reach", "clone graph".

**Common problems:** Number of Islands (M), Clone Graph (M), Max Area of Island (M), Pacific Atlantic (M).

**Complexity:** Time O(V+E) (grid: O(rows·cols)), Space O(V).

## Pattern 2 — BFS (shortest path in unweighted graph/grid)

**Ask yourself:** *"Do I need the shortest number of steps / fewest moves?"*

**Mental model:** Ripples spreading from a stone — everything at distance 1 is reached before distance 2.

**Code template**
```python
from collections import deque
def shortest(grid, start, target):
    q = deque([(start, 0)])          # (node, distance)
    seen = {start}
    while q:
        node, dist = q.popleft()
        if node == target: return dist
        for nxt in neighbors(node):
            if nxt not in seen:
                seen.add(nxt)        # mark on enqueue, not dequeue
                q.append((nxt, dist + 1))
    return -1
```

**When to use it:** "shortest path", "fewest steps", "minimum moves", "rotting oranges", "word ladder".

**Common problems:** Rotting Oranges (M), Word Ladder (H), Shortest Path in Binary Matrix (M).

**Complexity:** Time O(V+E), Space O(V).

**Mistakes:** marking visited on dequeue instead of enqueue (causes duplicates/TLE); using DFS for shortest path (DFS doesn't give shortest in general).

🧠 **Recognition Checklist — Graphs BFS/DFS**
- If "explore whole region / count components / flood fill" → **DFS (or BFS)**.
- If "shortest / fewest steps in unweighted" → **BFS**.
- Grid problems → treat cells as nodes, 4 (or 8) directions as edges.
- Always → **visited set**. Weighted shortest path → **Dijkstra** (topic 24).

🔥 **Must-Know Problems**
1. Number of Islands (M)
2. Clone Graph (M)
3. Max Area of Island (M)
4. Rotting Oranges (M)
5. Pacific Atlantic Water Flow (M)
6. Word Ladder (H, stretch)

---

# 14. Topological Sort 🟡

Ordering nodes of a **DAG** (directed acyclic graph) so every edge points "forward". The go-to for **dependencies / prerequisites**.

## Pattern 1 — Kahn's algorithm (BFS on in-degrees)

**Ask yourself:** *"Is this 'do X before Y' — an ordering with prerequisites? Could there be a cycle?"*

**Mental model:** Course scheduling — you can only take a class once all its prerequisites are done. Repeatedly take everything with zero remaining prerequisites.

**Code template**
```python
from collections import deque, defaultdict
def topo_order(n, edges):            # edges: (a -> b) means a before b
    graph = defaultdict(list)
    indeg = [0] * n
    for a, b in edges:
        graph[a].append(b)
        indeg[b] += 1
    q = deque(i for i in range(n) if indeg[i] == 0)
    order = []
    while q:
        node = q.popleft()
        order.append(node)
        for nxt in graph[node]:
            indeg[nxt] -= 1          # one prereq satisfied
            if indeg[nxt] == 0:
                q.append(nxt)
    return order if len(order) == n else []   # [] means a cycle exists
```

**When to use it:** "course schedule", "build order", "task dependencies", "can finish", "alien dictionary".

**Common problems:** Course Schedule (M), Course Schedule II (M), Alien Dictionary (H).

**Complexity:** Time O(V+E), Space O(V+E).

**Mistakes:** not detecting cycles (if `len(order) != n`, there's a cycle → impossible); mixing up edge direction.

🧠 **Recognition Checklist — Topological Sort**
- If "prerequisite / must come before / dependency / build order" → **topological sort**.
- If "can this ordering even be done?" → topo + **cycle check**.
- Directed + acyclic ordering → **Kahn's (in-degree BFS)** or DFS post-order.

🔥 **Must-Know Problems**
1. Course Schedule (M)
2. Course Schedule II (M)
3. Alien Dictionary (H, stretch)

---

# 15. Union Find / Disjoint Set 🟡

Tracks which items are in the **same group**, with near-O(1) "union" and "find". Great for connectivity when edges arrive over time.

## Pattern 1 — Group membership / connectivity

**Ask yourself:** *"Am I repeatedly merging groups and asking 'are these two connected?'"*

**Mental model:** Each group has a "captain" (root). To merge groups, point one captain at the other. Two items are connected if they share a captain.

**Code template**
```python
class DSU:
    def __init__(self, n):
        self.parent = list(range(n))
    def find(self, x):
        while self.parent[x] != x:
            self.parent[x] = self.parent[self.parent[x]]  # path compression
            x = self.parent[x]
        return x
    def union(self, a, b):
        ra, rb = self.find(a), self.find(b)
        if ra == rb: return False    # already together (a cycle if edge)
        self.parent[ra] = rb
        return True
```

**When to use it:** "connected components (dynamic)", "redundant connection / detect cycle in undirected graph", "number of provinces", "accounts merge".

**Common problems:** Number of Provinces (M), Redundant Connection (M), Number of Connected Components (M), Accounts Merge (M).

**Complexity:** ~O(α(n)) ≈ O(1) per op with path compression, Space O(n).

**Mistakes:** forgetting path compression (slow); using DSU when a simple DFS on a static graph is clearer.

🧠 **Recognition Checklist — Union Find**
- If "are these connected?" asked **repeatedly as edges are added** → **DSU**.
- If "detect a cycle in an undirected graph" → **union returns False**.
- If "count groups / provinces / components" → **count distinct roots**.
- Static one-shot connectivity → plain **DFS/BFS** is fine too.

🔥 **Must-Know Problems**
1. Number of Provinces (M)
2. Number of Connected Components (M)
3. Redundant Connection (M)
4. Accounts Merge (M)

---

# 16. Trie (Prefix Tree) 🟡

A tree where each path spells a word, char by char. Makes **prefix** queries fast.

## Pattern 1 — Insert & search by prefix

**Ask yourself:** *"Am I doing lots of 'starts with / is this word present' on a set of strings?"*

**Mental model:** A dictionary organized by letters — follow letters down branches; a word ends where a node is flagged.

**Code template**
```python
class Trie:
    def __init__(self):
        self.root = {}
    def insert(self, word):
        node = self.root
        for c in word:
            node = node.setdefault(c, {})
        node['#'] = True             # end-of-word marker
    def search(self, word, prefix=False):
        node = self.root
        for c in word:
            if c not in node: return False
            node = node[c]
        return True if prefix else '#' in node
```

**When to use it:** "prefix", "autocomplete", "dictionary of words", "word search II", "search with wildcard".

**Common problems:** Implement Trie (M), Design Add and Search Words (M), Word Search II (H).

**Complexity:** insert/search O(L) where L = word length. Space O(total chars).

**Mistakes:** forgetting the end-of-word marker (so "app" wrongly matches when only "apple" was inserted); using a trie when a plain `set` of words suffices (only worth it for prefix work).

🧠 **Recognition Checklist — Trie**
- If "prefix / startsWith / autocomplete" → **trie**.
- If "many words + repeated prefix lookups" → **trie**.
- If "word search on a grid with a word list" → **trie + backtracking**.
- If you only need exact membership → a **set** is simpler.

🔥 **Must-Know Problems**
1. Implement Trie (Prefix Tree) (M)
2. Design Add and Search Words Data Structure (M)
3. Word Search II (H, stretch)

---

# 17. Dynamic Programming — the mindset 🟢

DP = **break a problem into overlapping subproblems and reuse their answers.** Two signals scream DP:
1. "Count the number of ways" / "min or max cost" / "is it possible", **and**
2. the same subproblem shows up again and again.

**Two ways to write every DP:**
- **Top-down (memoization):** recursion + a cache. Easiest to derive.
- **Bottom-up (tabulation):** fill a table iteratively. Often faster, no recursion limit.

**The universal recipe:**
1. Define the **state**: what does `dp[i]` (or `dp[i][j]`) *mean*?
2. Find the **recurrence**: how does a state depend on smaller states?
3. Set **base cases**.
4. Decide **iteration order** (so dependencies are ready).

**Memoization skeleton**
```python
from functools import lru_cache
@lru_cache(None)
def solve(state):
    if base_case(state): return base_value
    return combine(solve(smaller_state_1), solve(smaller_state_2))
```

**Common mistake:** jumping to code before writing down what the state *means* in one English sentence. Do that first, always.

---

# 18. 1D Dynamic Programming 🟢

State is a single index. `dp[i]` = the answer considering the first `i` items (or ending at `i`).

## Pattern 1 — "Answer at i depends on a few previous answers"

**Ask yourself:** *"Can I compute `dp[i]` from `dp[i-1]`, `dp[i-2]`, …?"*

**Mental model:** Climbing stairs — the ways to reach step i = ways to i-1 (take 1) + ways to i-2 (take 2).

**Code template**
```python
def climb(n):
    a, b = 1, 1                  # dp[i-2], dp[i-1]
    for _ in range(n):
        a, b = b, a + b          # dp[i] = dp[i-1] + dp[i-2]
    return a
```

**When to use it:** "ways to reach", "climbing stairs", "house robber", "min cost", "decode ways", "Fibonacci-like".

**Common problems:** Climbing Stairs (E), House Robber (M), Min Cost Climbing Stairs (E), Decode Ways (M).

**Complexity:** Time O(n), Space O(1) (rolling variables) or O(n).

**Example** — House Robber `[2,7,9,3]`: `dp[i]=max(dp[i-1], dp[i-2]+nums[i])` → 2,7,11,11 → **11**.

## Pattern 2 — "Best subsequence ending here" (choose extend or restart)

**Ask yourself:** *"For each position, is the best answer 'extend the previous' or 'start fresh here'?"*

**Code template**
```python
def max_subarray(nums):          # Kadane
    cur = best = nums[0]
    for x in nums[1:]:
        cur = max(x, cur + x)    # restart at x, or extend
        best = max(best, cur)
    return best
```

**When to use it:** "maximum subarray sum", "longest increasing subsequence", "max product".

**Common problems:** Maximum Subarray (M), Longest Increasing Subsequence (M), Maximum Product Subarray (M), Word Break (M).

**Complexity:** O(n) for Kadane; O(n log n) for LIS optimized.

🧠 **Recognition Checklist — 1D DP**
- If "ways / min / max over a sequence" + reuse → **1D DP**.
- If `dp[i]` depends on the last 1–2 values → **rolling variables** (O(1) space).
- If "best ending here, extend vs restart" → **Kadane-style**.
- If "can I build the string/number" → **dp over prefixes** (Word Break, Decode Ways).

🔥 **Must-Know Problems**
1. Climbing Stairs (E)
2. Min Cost Climbing Stairs (E)
3. House Robber (M)
4. Maximum Subarray (M)
5. Longest Increasing Subsequence (M)
6. Word Break (M)
7. Coin Change (M)

---

# 19. 2D Dynamic Programming 🟡

State needs **two indices** — usually two sequences, or a grid, or (index, capacity). `dp[i][j]` compares/combines two dimensions.

## Pattern 1 — Two sequences (compare i vs j)

**Ask yourself:** *"Am I matching/aligning two strings or arrays?"*

**Mental model:** Aligning two words letter by letter; at each cell you decide match, skip-from-A, or skip-from-B.

**Code template**
```python
def lcs(a, b):                   # longest common subsequence
    m, n = len(a), len(b)
    dp = [[0]*(n+1) for _ in range(m+1)]
    for i in range(1, m+1):
        for j in range(1, n+1):
            if a[i-1] == b[j-1]:
                dp[i][j] = dp[i-1][j-1] + 1          # chars match
            else:
                dp[i][j] = max(dp[i-1][j], dp[i][j-1])  # skip one
    return dp[m][n]
```

**When to use it:** "longest common subsequence", "edit distance", "is subsequence", "interleaving".

**Common problems:** Longest Common Subsequence (M), Edit Distance (H), Distinct Subsequences (H).

**Complexity:** Time O(m·n), Space O(m·n) (or O(n) with rolling rows).

## Pattern 2 — Grid paths

**Ask yourself:** *"Am I moving through a grid, combining answers from the cells I could come from?"*

**Code template**
```python
def min_path_sum(grid):
    m, n = len(grid), len(grid[0])
    dp = [[0]*n for _ in range(m)]
    for i in range(m):
        for j in range(n):
            if i == 0 and j == 0: dp[i][j] = grid[i][j]
            elif i == 0:          dp[i][j] = dp[i][j-1] + grid[i][j]
            elif j == 0:          dp[i][j] = dp[i-1][j] + grid[i][j]
            else: dp[i][j] = min(dp[i-1][j], dp[i][j-1]) + grid[i][j]
    return dp[-1][-1]
```

**When to use it:** "unique paths", "min path sum", "grid with obstacles", "maximal square".

**Common problems:** Unique Paths (M), Minimum Path Sum (M), Maximal Square (M), 0/1 Knapsack (M).

**Complexity:** Time O(m·n), Space O(m·n) → O(n) with a rolling row.

**Mistakes:** off-by-one with the `+1` sized table; wrong iteration order; forgetting base row/column.

🧠 **Recognition Checklist — 2D DP**
- If "two strings/arrays compared" → **dp[i][j] over both**.
- If "grid path / min-max to reach cell" → **grid DP**.
- If "(items, capacity/budget)" → **knapsack-style 2D DP**.
- Space tight? → most 2D DPs reduce to **one or two rolling rows**.

🔥 **Must-Know Problems**
1. Unique Paths (M)
2. Minimum Path Sum (M)
3. Longest Common Subsequence (M)
4. Coin Change II (M)
5. Maximal Square (M)
6. Edit Distance (H, stretch)

---

# 20. Bit Manipulation 🟡

Working with the binary bits of integers. A handful of tricks cover almost all interview uses.

## Pattern 1 — The core bit tricks

**Ask yourself:** *"Is this about parity, toggling, uniqueness without extra space, or powers of two?"*

**Cheat sheet**
```python
x & 1                 # is x odd?  (lowest bit)
x >> 1                # divide by 2
x << 1                # multiply by 2
x & (x - 1)           # drop the lowest set bit  → count bits, power-of-two check
x ^ y                 # XOR: equal→0, and a^a==0, a^0==a
```

**Mental model:** XOR is a light switch — flip twice and you're back. So pairs cancel; the lone value survives.

**Code template — find the single number**
```python
def single(nums):
    res = 0
    for x in nums:
        res ^= x         # pairs cancel to 0, unique remains
    return res
```

**When to use it:** "appears once/twice", "without extra memory", "power of two", "count set bits", "missing number".

**Common problems:** Single Number (E), Number of 1 Bits (E), Counting Bits (E), Missing Number (E), Reverse Bits (E).

**Complexity:** Time O(n) or O(1), Space O(1).

**Mistakes:** forgetting XOR self-cancels; Python ints are unbounded (no fixed 32-bit overflow — mask with `& 0xFFFFFFFF` when a problem assumes 32-bit).

🧠 **Recognition Checklist — Bit Manipulation**
- If "one number appears once, rest twice" → **XOR everything**.
- If "power of two" → **`x > 0 and x & (x-1) == 0`**.
- If "count set bits" → **`x &= x-1` loop**.
- If "O(1) space + integers" hint → think **bits**.

🔥 **Must-Know Problems**
1. Single Number (E)
2. Number of 1 Bits (E)
3. Counting Bits (E)
4. Missing Number (E)
5. Reverse Bits (E)

---

# 21. Math / Number Theory 🟡

Usually a specific insight rather than a data structure. Know a few classics; don't over-invest.

## Pattern 1 — Common math tools

**Ask yourself:** *"Is there a formula, digit trick, or primes/GCD idea that collapses the brute force?"*

**Toolbox**
```python
import math
math.gcd(a, b)                          # greatest common divisor
# sieve of Eratosthenes: all primes < n
def sieve(n):
    is_p = [True] * n
    is_p[0:2] = [False, False]
    for i in range(2, int(n**0.5) + 1):
        if is_p[i]:
            for j in range(i*i, n, i):
                is_p[j] = False
    return [i for i, p in enumerate(is_p) if p]
```

**When to use it:** "count primes", "gcd/lcm", "digits of a number", "roman numerals", "fast power", "happy number".

**Common problems:** FizzBuzz (E), Palindrome Number (E), Count Primes (M), Pow(x,n) (M), Happy Number (E).

**Complexity:** varies — sieve O(n log log n), fast power O(log n).

**Mistakes:** integer overflow assumptions (fine in Python); reinventing `math.gcd`; brute-forcing primes instead of the sieve.

🧠 **Recognition Checklist — Math**
- If "primes up to n" → **sieve**.
- If "reduce fraction / repeated pattern" → **gcd**.
- If "x^n efficiently" → **fast exponentiation (halve n)**.
- If it feels like a formula problem → look for the **closed form** before coding loops.

🔥 **Must-Know Problems**
1. FizzBuzz (E)
2. Palindrome Number (E)
3. Happy Number (E)
4. Count Primes (M)
5. Pow(x, n) (M)

---

# 22. Prefix Sum 🟢

Precompute cumulative sums so any **range sum** is O(1). The trick that makes many subarray problems linear.

## Pattern 1 — Range sum via prefixes

**Ask yourself:** *"Do I need sums of many subarrays / a running total?"*

**Mental model:** Mile markers on a road — distance between two points = marker[b] − marker[a].

**Code template**
```python
def range_sum(nums):
    prefix = [0]
    for x in nums:
        prefix.append(prefix[-1] + x)
    # sum of nums[i..j] inclusive:
    def query(i, j): return prefix[j+1] - prefix[i]
    return query
```

**When to use it:** "sum of range", "immutable range queries", "subarray sum".

**Common problems:** Range Sum Query Immutable (E), Running Sum (E).

**Complexity:** build O(n), each query O(1).

## Pattern 2 — Prefix sum + hashmap ("have I seen a prefix that makes target?")

**Ask yourself:** *"Count subarrays whose sum equals k?"*

**Plain language:** Running sum `S`. A subarray summing to `k` ends here iff we've seen a prefix `S - k` before. Count those.

**Code template**
```python
def subarray_sum(nums, k):
    seen = {0: 1}            # prefix sum 0 seen once (empty prefix)
    s = count = 0
    for x in nums:
        s += x
        count += seen.get(s - k, 0)   # prefixes that complete a k-subarray
        seen[s] = seen.get(s, 0) + 1
    return count
```

**When to use it:** "count subarrays sum = k", "subarray divisible by k", "contiguous with property".

**Common problems:** Subarray Sum Equals K (M), Contiguous Array (M), Subarray Sums Divisible by K (M).

**Complexity:** Time O(n), Space O(n).

**Mistakes:** forgetting the `{0:1}` seed; confusing prefix-sum (contiguous) with subsequence problems.

🧠 **Recognition Checklist — Prefix Sum**
- If "many range-sum queries" → **prefix array**.
- If "count/find subarray with sum = k" → **prefix sum + hashmap**.
- If "subarray divisible by k / equal 0s and 1s" → **prefix sum on remainders/counts**.

🔥 **Must-Know Problems**
1. Running Sum of 1d Array (E)
2. Range Sum Query — Immutable (E)
3. Subarray Sum Equals K (M)
4. Contiguous Array (M)
5. Product of Array Except Self (M — prefix/suffix)

---

# 23. Monotonic Stack 🟢

A stack kept **sorted** (increasing or decreasing). The classic answer to **"next greater / smaller element"** in O(n).

## Pattern 1 — Next greater / smaller element

**Ask yourself:** *"For each element, do I need the nearest bigger/smaller one to its right (or left)?"*

**Plain language:** Keep a stack of items still "waiting" for their answer. When a new item resolves the ones on top, pop them and record.

**Mental model:** People in a queue waiting to see someone taller pass by; when a tall person arrives, everyone shorter who was waiting gets their answer at once.

**Code template**
```python
def next_greater(nums):
    res = [-1] * len(nums)
    stack = []                       # holds indices, values decreasing
    for i, x in enumerate(nums):
        while stack and nums[stack[-1]] < x:
            res[stack.pop()] = x     # x is the next greater for that index
        stack.append(i)
    return res
```

**When to use it:** "next greater/smaller", "daily temperatures", "stock span", "largest rectangle in histogram", "trapping rain water".

**Common problems:** Daily Temperatures (M), Next Greater Element I (E), Largest Rectangle in Histogram (H), Trapping Rain Water (H).

**Complexity:** Time O(n) (each index pushed/popped once), Space O(n).

**Example** — Daily Temperatures `[73,74,75,71,76]` → wait for warmer: `[1,1,3,1,0]`.

**Mistakes:** storing values when you need indices (for distances); wrong comparison direction (`<` vs `>`) for greater vs smaller.

🧠 **Recognition Checklist — Monotonic Stack**
- If "next greater/smaller element" → **monotonic stack**.
- If "how many days/steps until a bigger value" → **monotonic stack of indices**.
- If "histogram rectangle / trapped water" → **monotonic stack**.
- Brute force is O(n²) scanning right → the stack makes it O(n).

🔥 **Must-Know Problems**
1. Next Greater Element I (E)
2. Daily Temperatures (M)
3. Online Stock Span (M)
4. Largest Rectangle in Histogram (H, stretch)
5. Trapping Rain Water (H, stretch)

---

# 24. Advanced Graph Algorithms 🔴

Lower priority for internships — learn after the essentials. Know *when* they apply more than memorizing every detail.

## Pattern 1 — Dijkstra (shortest path, weighted, non-negative)

**Ask yourself:** *"Shortest path where edges have different positive costs?"* (BFS only handles equal weights.)

**Mental model:** BFS with a priority queue — always expand the currently-cheapest node.

**Code template**
```python
import heapq
def dijkstra(graph, start):          # graph: {node: [(nbr, weight), ...]}
    dist = {start: 0}
    heap = [(0, start)]
    while heap:
        d, node = heapq.heappop(heap)
        if d > dist.get(node, float('inf')): continue
        for nbr, w in graph[node]:
            nd = d + w
            if nd < dist.get(nbr, float('inf')):
                dist[nbr] = nd
                heapq.heappush(heap, (nd, nbr))
    return dist
```

**When to use it:** "shortest/cheapest path with weights", "network delay", "cheapest flights (k stops)".

**Common problems:** Network Delay Time (M), Cheapest Flights Within K Stops (M — Dijkstra/Bellman-Ford), Path With Minimum Effort (M).

**Complexity:** Time O(E log V), Space O(V).

## Pattern 2 — Minimum Spanning Tree (Prim / Kruskal) 🔴

**Ask yourself:** *"Connect all nodes at minimum total edge cost?"*

**When to use it:** "min cost to connect all points/cities". Kruskal = sort edges + **Union Find**; Prim = heap.

**Common problems:** Min Cost to Connect All Points (M), Connecting Cities (M).

**Complexity:** O(E log E).

**Also good-to-know (don't over-study):** Bellman-Ford (handles negative edges), Floyd-Warshall (all-pairs, tiny graphs).

🧠 **Recognition Checklist — Advanced Graphs**
- If "shortest path" + **weighted, positive** → **Dijkstra**.
- If "unweighted shortest" → plain **BFS** (don't over-engineer).
- If "connect everything cheapest" → **MST (Kruskal + Union Find)**.
- If "negative weights" → **Bellman-Ford**.

🔥 **Must-Know Problems**
1. Network Delay Time (M)
2. Cheapest Flights Within K Stops (M)
3. Min Cost to Connect All Points (M)
4. Path With Minimum Effort (M)

---

# 🚨 "I Don't Know What Pattern to Use" — Decision Tree

Open a new problem and walk down this:

```
START: read the problem, note the data type + what's asked.

1. Is it a CONTIGUOUS subarray / substring?
   ├─ YES → fixed length k?  ── YES → Sliding Window (fixed)
   │                          └─ NO  → longest/shortest w/ condition? → Sliding Window (variable)
   │                                   sum = k / count subarrays?     → Prefix Sum (+ hashmap)
   └─ NO → continue

2. Is the array/input SORTED (or can I sort it)?
   ├─ pair/triple with a target sum?   → Two Pointers
   ├─ searching for a value / boundary → Binary Search
   └─ "min/max value such that check()" → Binary Search on the Answer

3. Do I need FAST lookups ("seen it? how many? where?")?
   └─ YES → Hashmap / Hashset

4. Is it "next greater / smaller / warmer" per element?
   └─ YES → Monotonic Stack

5. Is it a TREE?
   ├─ levels / shortest depth → BFS
   └─ combine children        → DFS recursion
      (BST + sorted/kth)      → Inorder traversal

6. Is it a GRAPH / GRID (regions, connections, paths)?
   ├─ shortest steps, unweighted   → BFS
   ├─ explore region / components  → DFS  (dynamic connectivity → Union Find)
   ├─ prerequisites / ordering     → Topological Sort
   └─ weighted shortest path       → Dijkstra

7. "Generate ALL / every combination / permutation / arrangement"?
   └─ YES → Backtracking (choose → explore → un-choose)

8. "Count ways" / "min or max cost" / "is it possible", with overlapping subproblems?
   ├─ one index  → 1D DP
   ├─ two indices / grid / two strings → 2D DP
   └─ (greedy works only if local best ⇒ global best — verify!)

9. "Top k / kth largest / repeatedly need min-max"?
   └─ YES → Heap

10. "Prefix / startsWith / word dictionary"?
    └─ YES → Trie

11. "Appears once, powers of two, O(1) space with ints"?
    └─ YES → Bit Manipulation

Still stuck? → brute force first (get ANY working solution),
then ask "what work am I repeating?" — that repetition names the pattern.
```

---

# 📚 Recommended Study Order

Depth over quantity. Master a pattern's *recognition* before moving on. Rough target: ~2–4 easy then ~4–8 medium per topic, until you can spot the pattern within a minute of reading a new problem.

**Phase 1 — Foundations (start here) 🟢**
1. **Arrays & Hashing** — learn: seen-it / count-it / where-it. Do 3 Easy, 5 Medium. Ready when Two Sum & Group Anagrams feel automatic.
2. **Two Pointers** — learn: converging + slow/fast. 2 Easy, 4 Medium.
3. **Sliding Window** — learn: fixed vs variable, "expand right / shrink left". 2 Easy, 5 Medium. This is high-frequency — invest here.
4. **Stack** — learn: matching pairs. 2 Easy, 3 Medium.
5. **Binary Search** — learn: classic + on-the-answer. 2 Easy, 4 Medium.

**Phase 2 — Linked structures & trees 🟢**
6. **Linked Lists** — fast/slow, reverse, dummy head. 3 Easy, 4 Medium.
7. **Trees** — DFS recursion + BFS levels. 3 Easy, 5 Medium. (Very common in interviews.)
8. **BST** — inorder = sorted. 1 Easy, 3 Medium.
9. **Heap / Priority Queue** — top-k + live min/max. 4 Medium.

**Phase 3 — Graphs 🟢**
10. **Graphs BFS/DFS** — islands, components, shortest steps. 5 Medium. (Core.)
11. **Topological Sort** — course schedule. 2 Medium.
12. **Union Find** — connectivity, cycle detection. 3 Medium.

**Phase 4 — The big two: recursion & DP 🟢**
13. **Backtracking** — subsets/permutations/combinations. 4–5 Medium.
14. **1D DP** — climbing stairs → house robber → coin change. 2 Easy, 5 Medium. Go slow; write the *state sentence* every time.
15. **2D DP** — grid paths + two-string DP. 4 Medium. Move on once you can define the state unprompted.

**Phase 5 — Rounding out 🟡**
16. **Prefix Sum** — range sums + sum=k hashmap. 2 Easy, 2 Medium.
17. **Monotonic Stack** — next greater / daily temperatures. 1 Easy, 3 Medium.
18. **Intervals** — merge + rooms. 4 Medium.
19. **Greedy** — verify each with a counterexample. 4 Medium.
20. **Trie** — implement + prefix search. 2 Medium.

**Phase 6 — Only if time 🔴**
21. **Bit Manipulation** — the core tricks. 4 Easy.
22. **Math / Number Theory** — sieve, gcd, fast power. 3 Easy, 1 Medium.
23. **Advanced Graphs** — Dijkstra, then MST. 3 Medium. Recognize when they apply; don't grind them.

**Weekly rhythm that works:** pick ONE topic, read its section here, do the Easies to lock the template, then Mediums while **saying the pattern name out loud before coding**. Redo any problem you couldn't pattern-match within a week. When "🚨 I Don't Know What Pattern to Use" is rarely needed, you're interview-ready.

---

*End of handbook. Keep it open beside your practice — over time you'll lean on the checklists and the decision tree more than the templates.*
