# Binary Search Tree and Red Black Tree

**Author:** Eric Zhou  
**Email:** <eric_ds_zhou@outlook.com>  
**Date:** Oct 20, 2026

## Introduction

This technical note summarizes the tools and logic used in Binary Search Tree and Red Black Tree

## Table of contents

- What Is a Binary Search Tree?
- Basic Concepts
  - Sibling
  - Successor
  - Predecessor
  - Height
  - Minimum and Maximum Values
- Operations in a Binary Search Tree
  - Search
  - Insertion
  - Deletion
- What Is a Balanced BST?
- Rotations in a BST
  - Left Rotation
  - Right Rotation
- Advantages and Limitations of BSTs
  - Time Complexity of BST Operations
  - Balanced vs. Unbalanced BSTs
  - BST vs. Red-Black Tree: Time Complexity Comparison
- What Is a Red-Black Tree?
- Properties of a Valid Red-Black Tree
- Insertion in a Red-Black Tree
  - Case I: Parent and Uncle Are Both Red
    - Recoloring
  - Case II: Parent Is Red and Uncle Is Black
    - LR Case
    - RL Case
  - Case III: Parent Is Red and Uncle Is Black
    - LL Case
    - RR Case
  - Root Recoloring
- Practice Problem: BST Left Rotation and Balance Update (15 Points)
- Reference List

## What Is a Binary Search Tree?

A **Binary Search Tree (BST)** is a binary tree that satisfies the **Binary Search Tree Property**.

A binary tree is a tree with the following properties:

1. Every node has zero, one, or two children.
2. One node is designated as the **root**.
3. Every node except the root has exactly one parent.
4. A node with no children is called a **leaf**.

In a BST, for any node `x`:

- Every node `y` in the left subtree of `x` satisfies `y.key <= x.key`.
- Every node `y` in the right subtree of `x` satisfies `y.key >= x.key`.

This property must hold for **every node** in the tree, not just the root.

### Example

Consider the following Binary Search Tree:

```text
           15
          /  \
         6    18
        / \   / \
       3   8 17  20
      / \   \
     2   4   13
            /
           9
```

For example:

- All values in the left subtree of `15` are less than or equal to `15`.
- All values in the right subtree of `15` are greater than or equal to `15`.
- The node `13` is in the right subtree of `8`, since `13 > 8`.

An important property of a BST is that an **in-order traversal** visits all nodes in non-decreasing order.

For this tree, the in-order traversal produces:

`2, 3, 4, 6, 8, 9, 13, 15, 17, 18, 20`

The running time of an in-order traversal is:

$$
T(n) = \Theta(n)
$$

because every node is visited exactly once.

## Basic Concepts

### Sibling

In a binary tree, two nodes are called **siblings** if they share the same parent.

For a node `x`, its sibling can be found by examining its parent:

- If `x` is the left child, its sibling is the right child.
- If `x` is the right child, its sibling is the left child.
- If `x` is the root, it has no sibling.

If the parent has only one non-NIL child, the other child is `NIL`.

### Successor

The **successor** of a node `x` is the next node in the **in-order traversal** of the BST.

If all keys are distinct, the successor is the node with the smallest key greater than `x.key`.

There are two cases:

**Case I: `x` has a right subtree.**

The successor is the minimum node in the right subtree.

**Case II: `x` does not have a right subtree.**

The successor is the closest ancestor `y` such that `x` lies in the left subtree of `y`.

The algorithm is:

```text
TreeSuccessor(x)
    if x.right != NIL
        return TreeMin(x.right)
    end if

    y = x.parent

    while y != NIL and x == y.right
        x = y
        y = y.parent
    end while

    return y
```

**Time Complexity:**

In Case I, finding the minimum node takes at most O(h) time.

In Case II, the algorithm may traverse upward through the entire height of the tree.

Therefore, the worst-case running time is:

$$
T(h) = O(h)
$$

### Predecessor

The **predecessor** of a node `x` is the previous node in the **in-order traversal** of the BST.

If all keys are distinct, the predecessor is the node with the largest key smaller than `x.key`.

There are two symmetric cases:

**Case I: `x` has a left subtree.**

The predecessor is the maximum node in the left subtree.

**Case II: `x` does not have a left subtree.**

The predecessor is the closest ancestor `y` such that `x` lies in the right subtree of `y`.

The worst-case running time is:

$$
T(h) = O(h)
$$

### Height

The **height** of a binary tree is the number of edges on the longest path from the root to a leaf.

For example:

```text
           15
          /  \
         6    18
        / \
       3   8
            \
             13
            /
           9
```

The longest path is:

`15 -> 6 -> 8 -> 13 -> 9`

This path contains 4 edges, so the height of the tree is:

$$
h = 4
$$

For a BST containing `n` nodes, the height satisfies:

$$
\log_2(n+1)-1 \leq h \leq n-1
$$

The minimum height occurs when the tree is as balanced as possible.

The maximum height occurs when the BST degenerates into a chain.

Therefore:

- A balanced BST can have height Θ(log n).
- An unbalanced BST can have height Θ(n) in the worst case.

Since many BST operations follow a single path from the root to a node, their running times depend on the height of the tree.

### Minimum and Maximum Values

In a BST, the **minimum value** is found at the leftmost node, while the **maximum value** is found at the rightmost node.

**Finding the Minimum Value**

Starting from the root, repeatedly follow the left child until reaching a node with no left child.

```text
TreeMin(x)
    while x.left != NIL
        x = x.left
    end while

    return x
```

**Finding the Maximum Value**

Similarly, repeatedly follow the right child until reaching a node with no right child.

```text
TreeMax(x)
    while x.right != NIL
        x = x.right
    end while

    return x
```

**Time Complexity:**

Both algorithms follow a single downward path in the tree.

In the worst case, they may traverse the entire height of the BST.

Therefore:

$$
T(h) = O(h)
$$

For a BST whose height is Θ(log n), these operations take O(log n) time.

For a highly unbalanced BST, the worst-case running time can be Θ(n).

## Operations in a Binary Search Tree

The Binary Search Tree Property allows us to efficiently perform several fundamental operations, including **Search**, **Insertion**, and **Deletion**.

The running time of these operations generally depends on the height `h` of the BST.

### Search

To search for a key `K` in a BST, we begin at the root and compare `K` with the current node's key.

There are three possibilities:

1. If `K == x.key`, we have found the node.
2. If `K < x.key`, we continue searching in the left subtree.
3. If `K > x.key`, we continue searching in the right subtree.

If we reach `NIL`, the key does not exist in the BST.

**Pseudocode:**

```text
TreeSearch(x, K)
    if x == NIL or K == x.key
        return x
    else if K < x.key
        return TreeSearch(x.left, K)
    else
        return TreeSearch(x.right, K)
    end if
```

**Time Complexity:**

At each step, the algorithm moves down at most one level of the tree.

Therefore, the worst-case running time is:

$$
T(h) = O(h)
$$

For a balanced BST, searching takes O(log n) time in the worst case. However, for a highly unbalanced BST, it may take Θ(n) time.

### Insertion

To insert a new node `z` into a BST, we must find a valid position that preserves the Binary Search Tree Property.

The insertion process consists of two steps:

1. Find the future parent of `z`.
2. Insert `z` as the appropriate child of that parent.

**Step 1: Locate the Parent**

We begin at the root and follow the same comparison process used in Search.

```text
LocateParent(T, z)
    y = NIL
    x = T.root

    while x != NIL
        y = x

        if z.key < x.key
            x = x.left
        else
            x = x.right
        end if
    end while

    return y
```

Here, `y` keeps track of the most recently visited node, which will become the parent of `z`.

**Step 2: Insert the Node**

After locating the parent, we update the pointers to attach the new node.

```text
TreeInsert(T, z)
    y = LocateParent(T, z)
    z.parent = y

    if y == NIL
        T.root = z
    else if z.key < y.key
        y.left = z
    else
        y.right = z
    end if
```

If the BST is initially empty, `y == NIL`, and the new node becomes the root.

Otherwise, the new node is inserted as either the left or right child of `y`.

**Time Complexity:**

Locating the parent takes O(h) time, while updating the pointers takes Θ(1) time.

Therefore:

$$
T(h) = O(h) + \Theta(1) = O(h)
$$

### Deletion

Deleting a node from a BST is more complicated because we must preserve the Binary Search Tree Property after removing the node.

There are three primary cases.

**Case I: The Node Has No Children**

If node `z` is a leaf, we can simply remove it from the tree.

**Case II: The Node Has One Child**

If node `z` has exactly one child, we replace `z` with its child.

**Case III: The Node Has Two Children**

If node `z` has two children, we replace it with its **successor** `y`, which is the minimum node in the right subtree of `z`.

Since `y` is the minimum node in that subtree, it cannot have a left child.

Therefore, if `y` is not the direct right child of `z`, we can replace `y` with its right child before moving `y` into the position originally occupied by `z`.

#### Transplant

Before implementing Deletion, we introduce a helper function called `Transplant`.

The purpose of `Transplant(T, u, v)` is to replace the subtree rooted at `u` with the subtree rooted at `v`.

```text
Transplant(T, u, v)
    p = u.parent

    if p == NIL
        T.root = v
    else if u == p.left
        p.left = v
    else
        p.right = v
    end if

    if v != NIL
        v.parent = p
    end if
```

Notice that `Transplant` only updates the pointers connecting the replacement subtree to the original tree.

**Time Complexity:**

Since `Transplant` only performs a constant number of pointer assignments:

$$
T(n) = \Theta(1)
$$

#### TreeDelete

Using `Transplant`, we can implement the complete BST deletion algorithm.

```text
TreeDelete(T, z)
    if z.left == NIL
        Transplant(T, z, z.right)

    else if z.right == NIL
        Transplant(T, z, z.left)

    else
        y = TreeMin(z.right)

        if y != z.right
            Transplant(T, y, y.right)
            y.right = z.right
            y.right.parent = y
        end if

        Transplant(T, z, y)
        y.left = z.left
        y.left.parent = y
    end if
```

The first two conditions handle nodes with zero or one child.

The final case handles nodes with two children by finding their successors and updating the necessary pointers.

**Time Complexity:**

Finding the successor using `TreeMin` takes O(h) time.

All calls to `Transplant` and the remaining pointer assignments take Θ(1) time.

Therefore, the worst-case running time of Deletion is:

$$
T(h) = O(h)
$$

### Summary of Time Complexities

| Operation | Best Case | Worst Case |
| --- | --- | --- |
| Search | Θ(1) | O(h) |
| Insertion | Θ(1) | O(h) |
| Deletion (given node) | Θ(1) | O(h) |

For a balanced BST, `h = Θ(log n)`, so all three operations have an O(log n) worst-case running time.

However, for a highly unbalanced BST, `h` can be Θ(n), resulting in a Θ(n) worst-case running time.

## What Is a Balanced BST?

A **Balanced Binary Search Tree** is a BST whose structure prevents its height from becoming excessively large relative to the number of nodes.

Recall that for a BST containing `n` nodes, its height `h` satisfies:

$$
\log_2(n+1)-1 \leq h \leq n-1
$$

The height of a BST depends on how its nodes are arranged.

### Balanced BST

Consider the following BST:

```text
          8
        /   \
       4     12
      / \   /  \
     2   6 10  14
```

This tree has 7 nodes and a height of 2.

Since the nodes are distributed evenly across the left and right subtrees, the height remains relatively small.

For a height-balanced BST, the height satisfies:

$$
h = \Theta(\log n)
$$

Therefore, operations such as Search, Insertion, and Deletion have a worst-case running time of:

$$
T(n) = O(\log n)
$$

### Unbalanced BST

Now consider a BST containing the same values:

```text
    2
     \
      4
       \
        6
         \
          8
           \
            10
             \
              12
               \
                14
```

Although both trees contain exactly the same values, this BST has a height of 6.

In this case, the BST has degenerated into a chain, behaving similarly to a linked list.

In the worst case, the height of an unbalanced BST can be:

$$
h = \Theta(n)
$$

As a result, Search, Insertion, and Deletion can require:

$$
T(n) = \Theta(n)
$$

### Why Does Balance Matter?

The efficiency of most BST operations depends on the height of the tree.

A smaller height generally means fewer nodes need to be visited during an operation.

However, a regular BST does not automatically maintain a balanced structure after insertion or deletion.

To address this problem, we can use **tree rotations**, which rearrange nodes while preserving the Binary Search Tree Property.

This idea is fundamental to self-balancing BSTs, such as **Red-Black Trees**, which we will discuss later.

## Rotations in a BST

A **tree rotation** is a local operation that rearranges nodes in a Binary Search Tree while preserving the **Binary Search Tree Property**.

Rotations are commonly used to adjust the structure of a BST and are fundamental operations in self-balancing trees, such as Red-Black Trees.

There are two types of rotations:

- **Left Rotation**
- **Right Rotation**

### Left Rotation

A **Left Rotation** on a node `x` makes its right child `y` the new parent of `x`.

Consider the following BST:

```text
          x
         / \
        α   y
           / \
          β   γ
```

After performing `LeftRotate(T, x)`:

```text
          y
         / \
        x   γ
       / \
      α   β
```

Notice that:

1. `y` replaces `x` in the original tree.
2. `x` becomes the left child of `y`.
3. The left subtree of `y` (β) becomes the right subtree of `x`.
4. The subtree α and γ remain unchanged.

**Pseudocode:**

```text
LeftRotate(T, x)
    y = x.right
    x.right = y.left

    if y.left != NIL
        y.left.parent = x
    end if

    y.parent = x.parent

    if x.parent == NIL
        T.root = y
    else if x == x.parent.left
        x.parent.left = y
    else
        x.parent.right = y
    end if

    y.left = x
    x.parent = y
```

### Right Rotation

A **Right Rotation** is the symmetric operation of a Left Rotation.

A Right Rotation on a node `x` makes its left child `y` the new parent of `x`.

Consider the following BST:

```text
          x
         / \
        y   γ
       / \
      α   β
```

After performing `RightRotate(T, x)`:

```text
          y
         / \
        α   x
           / \
          β   γ
```

Notice that:

1. `y` replaces `x` in the original tree.
2. `x` becomes the right child of `y`.
3. The right subtree of `y` (β) becomes the left subtree of `x`.
4. The subtree α and γ remain unchanged.

**Pseudocode:**

```text
RightRotate(T, x)
    y = x.left
    x.left = y.right

    if y.right != NIL
        y.right.parent = x
    end if

    y.parent = x.parent

    if x.parent == NIL
        T.root = y
    else if x == x.parent.left
        x.parent.left = y
    else
        x.parent.right = y
    end if

    y.right = x
    x.parent = y
```

### Why Do Rotations Preserve the BST Property?

Although rotations change the parent-child relationships between nodes, they preserve the relative ordering of all keys.

For a Left Rotation, the original BST satisfies:

$$
\alpha \leq x \leq \beta \leq y \leq \gamma
$$

Here, α, β, and γ represent subtrees, and the inequalities describe the ordering of their keys.

Before the rotation, the in-order sequence is:

`α → x → β → y → γ`

After the rotation, the in-order sequence remains:

`α → x → β → y → γ`

Since the in-order sequence does not change, the Binary Search Tree Property is preserved.

The same argument applies to Right Rotation.

### Time Complexity

Both Left Rotation and Right Rotation involve only a constant number of pointer assignments.

No traversal of the tree is required.

Therefore, the running time of either rotation is:

$$
T(n) = \Theta(1)
$$

**Important:** A single rotation does not necessarily make the entire BST balanced. However, a sequence of rotations can be used to improve the tree's structure while preserving the BST Property.

## Advantages and Limitations of BSTs

A **Binary Search Tree (BST)** is an efficient data structure for storing and maintaining ordered data.

However, its performance depends heavily on the height of the tree. Although BSTs can provide efficient operations when balanced, their performance may degrade significantly when the tree becomes highly unbalanced.

### Advantages of BSTs

One of the main advantages of a BST is that it maintains its elements in a sorted structure.

Because of the **Binary Search Tree Property**, we can perform several operations efficiently without examining every node.

Some important advantages include:

1. **Efficient Searching:** We can eliminate an entire subtree after each comparison, allowing Search to follow only one path through the tree.
2. **Efficient Insertion and Deletion:** We can insert or delete nodes without shifting all the other elements.
3. **Ordered Traversal:** An in-order traversal visits all nodes in non-decreasing order.
4. **Successor and Predecessor:** We can efficiently find the next or previous node in the in-order sequence.

When the height of the BST is Θ(log n), most fundamental operations take O(log n) time in the worst case.

### Limitations of BSTs

The main limitation of a regular BST is that it does not guarantee a balanced structure.

For a BST containing `n` nodes, its height satisfies:

$$
\log_2(n+1)-1 \leq h \leq n-1
$$

In the worst case, the height can become:

$$
h = \Theta(n)
$$

For example, inserting elements in ascending order into an initially empty BST produces a chain rather than a balanced tree.

As a result, operations that normally take O(log n) time in a balanced BST may require Θ(n) time in the worst case.

Therefore, a regular BST cannot guarantee logarithmic running time for Search, Insertion, or Deletion.

### BST vs. Red-Black Tree: Time Complexity Comparison

A **Red-Black Tree (RBT)** is a self-balancing Binary Search Tree that uses additional coloring rules to control its height.

Unlike a regular BST, a Red-Black Tree guarantees that its height remains logarithmic in the number of nodes.

For a Red-Black Tree containing `n` internal nodes:

$$
\log_2(n+1)-1 \leq h \leq 2\log_2(n+1)
$$

Therefore:

$$
h = \Theta(\log n)
$$

The following table compares the worst-case running times of a regular BST and a Red-Black Tree.

| Operation | Regular BST | Red-Black Tree |
| --- | --- | --- |
| Search | O(h) | O(log n) |
| Insertion | O(h) | O(log n) |
| Deletion | O(h) | O(log n) |
| Minimum / Maximum | O(h) | O(log n) |
| Successor / Predecessor | O(h) | O(log n) |
| In-order Traversal | Θ(n) | Θ(n) |

For a regular BST, the running time of most operations depends on its height \(h\), which can be as large as $\Theta(n)$. In contrast, a Red-Black Tree guarantees $h = \Theta(\log n)$, ensuring logarithmic worst-case running time.

The major advantage of a Red-Black Tree is that it maintains a logarithmic height even after insertions and deletions.

This is achieved through **recoloring** and **tree rotations**, which preserve the BST Property while maintaining the structural constraints of a Red-Black Tree.

In the following sections, we will explore these properties and examine how Red-Black Trees maintain their balance.

## What Is a Red-Black Tree?

A **Red-Black Tree (RBT)** is a type of **self-balancing Binary Search Tree** that uses additional coloring rules to maintain a logarithmic height.

Recall that the height of a regular BST can be as large as $\Theta(n)$ in the worst case. As a result, operations such as Search, Insertion, and Deletion may require linear time.

To address this limitation, Red-Black Trees introduce an additional attribute, **color**, to each node.

Each node is assigned one of two colors:

- **Red**
- **Black**

These colors are used to enforce a set of structural properties that prevent the tree from becoming excessively unbalanced.

Unlike a regular BST, which does not guarantee a logarithmic height, a Red-Black Tree guarantees that its height remains proportional to the logarithm of the number of nodes.

For a Red-Black Tree containing `n` internal nodes, the height satisfies:

$$
\log_2(n+1)-1 \leq h \leq 2\log_2(n+1)
$$

Therefore:

$$
h = \Theta(\log n)
$$

This guarantees that fundamental operations such as Search and Insertion have a worst-case running time of:

$$
T(n) = O(\log n)
$$

A Red-Black Tree maintains its structural properties through two fundamental operations:

1. **Recoloring:** Changing the colors of certain nodes to restore the Red-Black Tree Properties.
2. **Tree Rotations:** Rearranging nodes while preserving the Binary Search Tree Property.

An important distinction is that a Red-Black Tree does not need to be perfectly balanced. Instead, it maintains sufficient balance to guarantee logarithmic height.

In the following section, we will examine the five properties that define a valid Red-Black Tree.

## Properties of a Valid Red-Black Tree

A Red-Black Tree is a Binary Search Tree that satisfies **five additional properties**.

These properties ensure that the tree maintains a logarithmic height, even after insertions and deletions.

### Five Properties of a Red-Black Tree

**Property I: Every node is either red or black.**

Each node in a Red-Black Tree must have exactly one color: red or black.

**Property II: The root is black.**

The root of a Red-Black Tree must always be black.

During insertion, the root may temporarily become red. However, it must be recolored black before the insertion process is complete.

**Property III: Every leaf is NIL and is black.**

In a Red-Black Tree, every missing child is represented by a black `NIL` leaf.

These `NIL` leaves are often omitted from diagrams for simplicity, but they must still be considered when checking the Red-Black Tree Properties.

**Property IV: If a node is red, both of its children must be black.**

In other words, two red nodes cannot appear consecutively along a path.

However, two consecutive black nodes are allowed.

**Property V: Every path from a node to its descendant NIL leaves contains the same number of black nodes.**

This property ensures that all downward paths from the same node have an equal number of black nodes.

When counting black nodes, remember that `NIL` leaves are black and must be included.

### How to Verify a Valid Red-Black Tree

To determine whether a given tree is a valid Red-Black Tree, we need to check all five properties.

In particular, we should pay close attention to Properties IV and V.

**Example:**

Consider the following tree:

```text
          10(B)
         /     \
       5(R)    15(R)
      /   \    /   \
    3(B) 7(B) 12(B) 18(B)
```

Here, `(B)` represents black and `(R)` represents red.

We can verify that:

1. Every node is either red or black.
2. The root `10` is black.
3. All missing children are black `NIL` leaves.
4. Every red node has two black children.
5. Every path from the root to a `NIL` leaf contains the same number of black nodes.

For example:

- `10 → 5 → 3 → NIL`: 3 black nodes
- `10 → 5 → 7 → NIL`: 3 black nodes
- `10 → 15 → 12 → NIL`: 3 black nodes
- `10 → 15 → 18 → NIL`: 3 black nodes

Therefore, this is a **valid Red-Black Tree**.

### Why Is the Height of a Red-Black Tree Θ(log n)?

We know that the height of a regular BST can be as large as Θ(n).

However, the five Red-Black Tree Properties guarantee that its height remains Θ(log n).

To prove this, we introduce three theorems.

**Theorem I: Number of Nodes in a Complete Binary Tree**

A complete binary tree of height `h` contains:

$$
n = 2^{h+1}-1
$$

**Proof:**

The number of nodes at each level is:

$$
1, 2, 2^2, \ldots, 2^h
$$

Therefore:

$$
n = 1+2+2^2+\cdots+2^h
$$

Using the geometric series formula:

$$
n = 2^{h+1}-1
$$

Solving for `h`:

$$
h = \log_2(n+1)-1
$$

**Theorem II: Minimum Number of Internal Nodes in a Red-Black Tree**

A Red-Black Tree of height `h` contains at least:

$$
n \geq 2^{\lceil h/2 \rceil}-1
$$

internal nodes.

**Proof:**

Recall that a red node cannot have a red child.

Therefore, along the longest path from the root to a leaf, red and black nodes can alternate, but two red nodes cannot appear consecutively.

Since the root is black, the number of black internal nodes along the longest path must be at least:

$$
\left\lceil \frac{h}{2} \right\rceil
$$

By Property V, every path from the root to a `NIL` leaf must contain the same number of black nodes.

Therefore, every root-to-leaf path contains at least this many black internal nodes.

This implies that the tree contains at least:

$$
2^{\lceil h/2 \rceil}-1
$$

internal nodes.

**Theorem III: Height of a Red-Black Tree**

For a Red-Black Tree containing `n` internal nodes, its height satisfies:

$$
h \leq 2\log_2(n+1)
$$

**Proof:**

From Theorem II:

$$
n \geq 2^{\lceil h/2 \rceil}-1
$$

Adding 1 to both sides:

$$
n+1 \geq 2^{\lceil h/2 \rceil}
$$

Taking logarithms:

$$
\log_2(n+1) \geq \left\lceil \frac{h}{2} \right\rceil
$$

Since:

$$
\left\lceil \frac{h}{2} \right\rceil \geq \frac{h}{2}
$$

we obtain:

$$
\log_2(n+1) \geq \frac{h}{2}
$$

Therefore:

$$
h \leq 2\log_2(n+1)
$$

Combining this with the lower bound for a Binary Search Tree:

$$
\log_2(n+1)-1 \leq h \leq 2\log_2(n+1)
$$

Thus:

$$
\boxed{h = \Theta(\log n)}
$$

This is why a Red-Black Tree can guarantee logarithmic worst-case running time for its fundamental operations.

## Insertion in a Red-Black Tree

Insertion in a Red-Black Tree begins in the same way as insertion in a regular Binary Search Tree.

However, after inserting a new node, we must ensure that all five Red-Black Tree Properties are satisfied.

The insertion process consists of three main steps:

1. Locate the appropriate position using the BST insertion procedure.
2. Insert the new node `z` and color it **red**.
3. Apply `RBInsertFixup` to restore the Red-Black Tree Properties.

**Why do we initially color the new node red?**

Because inserting a red node does not change the number of black nodes along any root-to-leaf path.

Therefore, Property V remains satisfied.

However, if the parent of `z` is also red, Property IV is violated, and we must fix the tree.

### Case I: Parent and Uncle Are Both Red

Suppose the newly inserted node `z` has a red parent, and its uncle (the sibling of its parent) is also red.

In this case, we can restore the local Red-Black Tree Properties through **recoloring**, without performing any rotations.

**Procedure:**

1. Color the parent of `z` black.
2. Color the uncle of `z` black.
3. Color the grandparent of `z` red.
4. Set `z` to its grandparent and repeat if necessary.

**Example:**

Before recoloring:

```text
           20(B)
          /     \
       10(R)    30(R)
       /
     5(R)
```

Here, the parent `10` and uncle `30` are both red.

After recoloring:

```text
           20(R)
          /     \
       10(B)    30(B)
       /
     5(R)
```

If `20` is the root, we must recolor it black at the end.

**Pseudocode:**

```text
RBInsertFixupA(T, z)
    while z != T.root and z.parent.color != black
        y = Sibling(z.parent)

        if y.color == black
            return
        end if

        z.parent.color = black
        y.color = black

        z = z.parent.parent
        z.color = red
    end while
```

**Time Complexity:**

Each iteration performs a constant number of operations and moves `z` upward by two levels.

Therefore, the loop runs at most `h/2` times, giving:

$$
T(h) = O(h)
$$

### Case II: Parent Is Red and Uncle Is Black

Case II occurs when the parent of `z` is red, the uncle is black, and the positions of `z`, its parent, and its grandparent form a **zigzag pattern**.

There are two symmetric configurations:

- LR (Left-Right)
- RL (Right-Left)

The purpose of Case II is to transform the configuration into Case III.

#### LR Case

The node `z` is the right child of its parent, while its parent is the left child of its grandparent.

Before rotation:

```text
           30(B)
          /
       10(R)
          \
          20(R)
```

We perform a **Left Rotation on the parent `10`**:

```text
           30(B)
          /
       20(R)
       /
    10(R)
```

The LR configuration has now been transformed into an LL configuration.

We then apply Case III.

#### RL Case

The node `z` is the left child of its parent, while its parent is the right child of its grandparent.

Before rotation:

```text
       10(B)
          \
          30(R)
          /
       20(R)
```

We perform a **Right Rotation on the parent `30`**:

```text
       10(B)
          \
          20(R)
             \
             30(R)
```

The RL configuration has now been transformed into an RR configuration.

We then apply Case III.

**Pseudocode:**

```text
RBInsertFixupB(T, z)
    if z == T.root or z.parent.color == black
        return
    end if

    x = z.parent
    w = x.parent

    if z == x.right and x == w.left
        z = x
        LeftRotate(T, x)

    else if z == x.left and x == w.right
        z = x
        RightRotate(T, x)
    end if
```

**Time Complexity:**

Case II requires only one rotation and a constant number of pointer assignments.

Therefore:

$$
T(n) = \Theta(1)
$$

### Case III: Parent Is Red and Uncle Is Black

Case III occurs when the parent of `z` is red, the uncle is black, and the positions of `z`, its parent, and its grandparent form a **straight-line pattern**.

There are two symmetric configurations:

- LL (Left-Left)
- RR (Right-Right)

Unlike Case II, Case III resolves the local violation through a rotation followed by recoloring.

#### LL Case

The node `z` is the left child of its parent, and its parent is the left child of its grandparent.

Before rotation:

```text
           30(B)
          /
       20(R)
       /
    10(R)
```

We perform a **Right Rotation on the grandparent `30`**:

```text
        20(R)
       /    \
    10(R)  30(B)
```

Then recolor: (Follow BRR)

- `20` becomes black.
- `30` becomes red.

The resulting tree is:

```text
        20(B)
       /    \
    10(R)  30(R)
```

#### RR Case

The node `z` is the right child of its parent, and its parent is the right child of its grandparent.

Before rotation:

```text
       10(B)
          \
          20(R)
             \
             30(R)
```

We perform a **Left Rotation on the grandparent `10`**:

```text
        20(R)
       /    \
    10(B)  30(R)
```

Then recolor: (Follow BRR)

- `20` becomes black.
- `10` becomes red.

The resulting tree is:

```text
        20(B)
       /    \
    10(R)  30(R)
```

**Pseudocode:**

```text
RBInsertFixupC(T, z)
    if z == T.root or z.parent.color == black
        return
    end if

    x = z.parent
    w = x.parent

    if z == x.left and x == w.left
        RightRotate(T, w)
        x.color = black
        w.color = red

    else if z == x.right and x == w.right
        LeftRotate(T, w)
        x.color = black
        w.color = red
    end if
```

**Time Complexity:**

Case III performs only one rotation and a constant number of recoloring operations.

Therefore:

$$
T(n) = \Theta(1)
$$

### Root Recoloring

During the insertion process, Case I may recolor the root red.

However, according to Property II, the root of a valid Red-Black Tree must always be black.

Therefore, after applying all necessary fixup operations, we explicitly recolor the root black.

**Complete Fixup Algorithm:**

```text
RBInsertFixup(T, z)
    RBInsertFixupA(T, z)
    RBInsertFixupB(T, z)
    RBInsertFixupC(T, z)
    T.root.color = black
```

The complete insertion algorithm is:

```text
RBTreeInsert(T, z)
    y = RBLocateParent(T, z)
    z.parent = y

    if y == NIL
        T.root = z
    else if z.key < y.key
        y.left = z
    else
        y.right = z
    end if

    z.left = leaf
    z.right = leaf
    z.color = red

    RBInsertFixup(T, z)
```

**Time Complexity:**

Locating the insertion position takes O(h) time.

The fixup operations require:

- Case I: O(h)
- Case II: O(1)
- Case III: O(1)
- Root Recoloring: O(1)

Therefore, the complete insertion algorithm takes:

$$
T(h) = O(h)
$$

Since a Red-Black Tree guarantees:

$$
h = \Theta(\log n)
$$

we obtain:

$$
\boxed{T(n) = \Theta(\log n)}
$$

## Practice Problem: BST Left Rotation and Balance Update (15 Points)

Consider a Binary Search Tree (BST) in which each node `v` stores the following information:

- `v.left`: Pointer to the left child
- `v.right`: Pointer to the right child
- `v.parent`: Pointer to the parent
- `v.height`: Height of the subtree rooted at `v`
- `v.balance`: Absolute difference between the heights of its left and right subtrees

The balance of a node is defined as:

$$
v.balance = |v.left.height - v.right.height|
$$

The height of a node is defined as:

$$
v.height = 1 + \max(v.left.height, v.right.height)
$$

Assume `NIL.height = 0`.

### Given Function

You are given the following function:

`LeftRotate(T, z)`

This function performs a standard left rotation at node `z` and correctly updates all parent and child pointers. However, it **does not update any height or balance values**.

### Task

Write pseudocode for a function:

`LeftRotateAndUpdateBalance(T, z)`

Your function must:

1. Perform a left rotation at node `z`.
2. Correctly update the `height` and `balance` of all potentially affected nodes.
3. Avoid unnecessarily traversing the entire tree.
4. Assume `z.right != NIL`.

You may use `max(a, b)` and `abs(a)`.

### Complexity Analysis

Analyze the **worst-case time complexity** of your algorithm in terms of the height \(h\) of the tree.

### Solution

```text
LeftRotateAndUpdateBalance(T, z) {
    LeftRotate(T, z)

    while (z != NIL) {
        z.height = 1 + max(z.left.height, z.right.height)
        z.balance = |z.left.height - z.right.height|
        z = z.parent()
    }
}
```

**Worst-case Time Complexity:**

$$
\boxed{\Theta(h)}
$$

After the left rotation, the algorithm updates the height and balance of each node along the path from the original `z` to the root. Each update takes constant time, and there are at most \(h + 1\) nodes on this path.

Therefore, the worst-case time complexity is \(\Theta(h)\).

## Reference List

1. *"CSE2331_Midterm_4_Review"* Created by Professor Painter.
2. *"CSE2331_Binary_Search_Tree_Homework"* Created by Professor Painter.
3. *"CSE2331_Binary_Search_Tree_Outline"* Created by Professor Painter.
