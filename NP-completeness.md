# NP-completeness

**Author:** Eric Zhou  
**Email:** <eric_ds_zhou@outlook.com>  
**Date:** Nov 14, 2026

## Introduction

This technical note summarizes the tools and logic used in NP-completeness

## Table of contents

- P, NP, NP-Complete, and NP-Hard
- Optimization vs. Decision Problems
- Common NP-Complete Problems
- Proving a Problem is in NP
- Proving a Problem is in P
- Polynomial-Time Reductions
  - Definition and Direction of Reduction
  - If A ≤p B and B ∈ P, then A ∈ P
  - If A ≤p B and A is NP-Complete, then B is NP-Hard
  - Transitivity of Reduction
- NP-Complete Problems in Detail
  - Boolean Satisfiability and 3-SAT
  - Independent Set# NP-Completeness

**Author:** Eric Zhou  
**Email:** <eric_ds_zhou@outlook.com>  
**Date:** Nov 14, 2026

## Introduction

This technical note follows the CSE 2331 NP-Completeness lecture, focusing on **P, NP, NP-Complete, NP-Hard**, how to verify solutions in polynomial time, and how to prove NP-Completeness using polynomial-time reductions. Most examples concern **decision problems**: questions with a YES or NO answer. We use lecture definitions, examples, and the five-step proof structure.

## Table of Contents

- P, NP, NP-Complete, and NP-Hard
- Optimization vs. Decision Problems
- Common NP-Complete Problems
- Proving a Problem is in NP
- Proving a Problem is in P
- Polynomial-Time Reductions
  - Definition and Direction of Reduction
  - If A ≤p B and B ∈ P, then A ∈ P
  - If A ≤p B and A is NP-Complete, then B is NP-Hard
  - Transitivity of Reduction
- NP-Complete Problems in Detail
  - Boolean Satisfiability and 3-SAT
  - Independent Set
  - Clique
    - Graph Complement
  - Vertex Cover
  - Set Cover
- Reduction Summary
- General Proof Template
- Examples
  - Proving a Problem is in NP
  - Constructing a Reduction
  - Proving NP-Completeness
- Key Takeaways

## P, NP, NP-Complete, and NP-Hard

### P

A decision problem is in **P** if it can be **solved** in polynomial time.

### NP

A decision problem is in **NP** if a proposed YES solution (a *certificate*) can be **verified** in polynomial time.

NP does **not** stand for “non-polynomial.” In the lecture, it refers to **nondeterministic polynomial**.

Since we can verify a solution by solving a problem when a polynomial-time solver exists,

$$
\boxed{P\subseteq NP}.
$$

### NP-Complete

A problem $Q$ is **NP-Complete** if both conditions hold:

1. $Q\in NP$.
2. Every problem in NP reduces to $Q$ in polynomial time.

In practice, once some problem $Q_1$ is already known to be NP-Complete, it is enough to prove

$$
Q_1\leq_p Q
$$

along with $Q\in NP$. This works because reductions are transitive.

### NP-Hard

A problem $Q$ is **NP-Hard** if every problem in NP reduces to $Q$ in polynomial time. Unlike an NP-Complete problem, it is **not required** to be in NP.

Thus,

$$
\boxed{\text{NP-Complete}=NP\cap\text{NP-Hard}}.
$$

If any NP-Complete problem could be solved in polynomial time, then $P=NP$. Whether $P=NP$ is unknown.

| Class | Polynomial-time solver required? | Polynomial-time YES verifier required? | Every NP problem reduces to it? |
| --- | --- | --- | --- |
| P | Yes | Yes | Not necessarily |
| NP | Not necessarily | Yes | Not necessarily |
| NP-Complete | Not known | Yes | Yes |
| NP-Hard | Not necessarily | Not necessarily | Yes |

## Optimization vs. Decision Problems

The lecture motivates NP-Completeness with the Travelling Salesman Problem (TSP).

**Optimization version:** Given a weighted graph and specified endpoints $v_1$ and $v_n$, find a minimum-weight path from $v_1$ to $v_n$ that visits every vertex exactly once.

**Decision version:** Given the graph, endpoints, and a cost bound $C$, does such a path exist with total cost **less than $C$**?

The lecture's example illustrates that choosing cheap edges greedily may fail: one proposed path has weight $41$, while another valid path has weight $36$.

Classical approaches mentioned in the lecture include:

- Brute force: approximately $O(n!)$.
- Held–Karp dynamic programming: $O(n^2 2^n)$.

Another related decision problem is **Hamiltonian Path**: Does a path exist from $v_1$ to $v_n$ visiting every vertex exactly once, without considering weights?

## Common NP-Complete Problems

The lecture lists the following **decision problems** as key examples. The input generally contains both the structure (graph, formula, or sets) and a target number.

| Problem | Decision question |
| --- | --- |
| Hamiltonian Path | Is there a path from $v_1$ to $v_n$ visiting every vertex exactly once? |
| 3-Coloring | Can we color the vertices using 3 colors so adjacent vertices have different colors? |
| Independent Set | Is there a set of $K$ vertices with no edges between selected vertices? |
| Clique | Is there a set of $K$ vertices with every pair connected? |
| Vertex Cover | Is there a set of $K$ vertices touching every edge? |
| Dominating Set | Is there a set of $K$ vertices such that every vertex is selected or adjacent to a selected vertex? |
| Longest Simple Path | Is there a simple path whose length exceeds a given distance $D$? |
| Subgraph Isomorphism | Does one graph occur as a subgraph of another? |
| SAT | Is there a truth assignment satisfying a Boolean expression? |
| Subset Sum | Is there a subset whose sum equals a given target $M$? |
| Set Cover | Can $K$ selected subsets cover the entire universe $U$? |
| Set Packing | Are there at least $K$ mutually disjoint sets in the collection? |
| Integer Programming | Is there an integer assignment satisfying all linear inequalities? |

The lecture also mentions **decision TSP**, formulated with a path cost strictly less than $C$. Its problem list includes a footnote labeling that particular TSP formulation **NP-Hard**; we retain that qualification rather than silently treating its listing as a formal proof of NP-Completeness.

The detailed proofs in this note concern **SAT/3-SAT, Independent Set, Clique, Vertex Cover, and Set Cover**.

## Proving a Problem is in NP

To show that a **decision problem $Q$ is in NP**, we do **not** have to find a solution efficiently. Instead, describe how to verify a proposed YES solution in polynomial time.

**Proof steps:**

1. **Certificate:** State what a proposed YES solution looks like (e.g. a vertex set, a set collection, or a truth assignment).
2. **Size/validity:** Verify that the proposed solution has the required form and required size.
3. **Conditions:** Check every condition specified by the problem.
4. **Complexity:** Show that all checks take polynomial time in the input size.
5. **Conclusion:** Therefore $Q\in NP$.

**Example — Clique.** Given a proposed set $S$ of $K$ vertices, first confirm $|S|=K$. Then check that **every pair** of vertices in $S$ is connected. There are at most

$$
\binom{K}{2}=\frac{K(K-1)}2=O(n^2)
$$

pairs for $K\le n$. With an adjacency matrix, each edge lookup is $O(1)$, so the pair checks take $O(n^2)$. Hence Clique is in NP. The lecture also gives an $O(mn^2)$ polynomial bound when checking edges less directly.

**Crucial distinction:** Verification assumes that the candidate solution is **already provided**. Searching for that candidate is a different task.

## Proving a Problem is in P

To show $Q\in P$, describe an algorithm that **solves the decision problem on every input** in polynomial time. Unlike an NP verification proof, this algorithm must find the answer without being handed a candidate YES solution.

**Proof steps:**

1. Describe the algorithm for deciding YES or NO.
2. Explain why its answer is correct for all valid inputs.
3. Analyze its worst-case runtime and show that it is polynomial, e.g. $O(n^k)$ for a **fixed** $k$.
4. Conclude that $Q\in P$ (and therefore $Q\in NP$).

**Example — Searching.** Given an array of length $n$ and a value $K$, examine each element until $K$ is found or the array ends. This answers whether $K$ exists in the array in $O(n)$ time. Thus this decision problem is in P.

**Example — Minimum Spanning Tree (decision version).** Given a connected weighted graph and a bound $C$, ask whether it has a spanning tree of weight **at most $C$**. Compute an MST using Prim's or Kruskal's algorithm, then compare its weight to $C$. These are polynomial-time algorithms, so the decision version is in P.

**Reminder from the lecture:** Trying all $K$-vertex subsets requires up to $O(K^2n^K)$ time. If $K$ is a **fixed constant**, this is polynomial in $n$, but if $K$ is part of the input then the exponent is variable; that method does **not** prove the general Independent Set problem is in P.

## Polynomial-Time Reductions

### Definition and Direction of Reduction

A **reduction** converts instances of one decision problem into instances of another while preserving yes/no answers.

We write

$$
Q_1 \leq_p Q_2
$$

when there is a polynomial-time mapping $f$ such that

$$
q_1 \text{ is YES} \iff f(q_1) \text{ is YES}.
$$

**Interpretation:** If we can efficiently solve $Q_2$, then we can efficiently solve $Q_1$ by first converting the input. In an NP-Completeness proof, the arrow goes **from a known NP-Complete problem to the problem we want to prove NP-Complete**.

A reduction is **not** an algorithm that directly solves $Q_2$. It is a way to **translate an instance of $Q_1$ into an instance of $Q_2$** so a solver for $Q_2$ could answer $Q_1$.

$$
\boxed{A\leq_p B\quad\text{means: solve }A\text{ using a solver for }B}
$$

For NP-Completeness, start with a problem **already known** to be NP-Complete ($A$) and reduce **toward** the target problem ($B$).

### If A ≤p B and B ∈ P, then A ∈ P

Suppose $A\leq_p B$ using a polynomial-time transformation $f$, and suppose $B$ has a polynomial-time decision algorithm.

1. Given an instance $a$ of $A$, compute $b=f(a)$ in polynomial time.
2. Run the polynomial-time algorithm for $B$ on $b$.
3. Return the same YES/NO answer, since $a$ is YES if and only if $b$ is YES.

Both stages are polynomial-time, so

$$
\boxed{A\leq_p B\ \land\ B\in P\ \Longrightarrow\ A\in P}.
$$

**Interpretation:** $B$ is at least as hard as $A$ under this reduction. If you could efficiently solve $B$, you could also efficiently solve $A$.

### If A ≤p B and A is NP-Complete, then B is NP-Hard

Since $A$ is NP-Complete, **every** NP problem $Q$ reduces to $A$. If $A\leq_p B$, then transitivity gives $Q\leq_p B$. Thus every NP problem reduces to $B$:

$$
\boxed{A\text{ NP-Complete}\ \land\ A\leq_p B\ \Longrightarrow\ B\text{ NP-Hard}}.
$$

If we **also prove $B\in NP$**, then

$$
\boxed{B\text{ is NP-Complete}}.
$$

Being NP-Hard alone is **not enough** to conclude NP-Complete.

### Transitivity of Reduction

If

$$
Q_1 \leq_p Q_2 \quad \text{and} \quad Q_2 \leq_p Q_3,
$$

then

$$
\boxed{Q_1 \leq_p Q_3}.
$$

Compose the mappings: $f_3(q)=f_2(f_1(q))$. A composition of polynomial-time mappings is polynomial-time.

## NP-Complete Problems in Detail

### Boolean Satisfiability and 3-SAT

The **Boolean Satisfiability (SAT)** problem asks whether Boolean variables can be assigned TRUE or FALSE so the entire expression evaluates to TRUE.

**Cook's Theorem (1971):** SAT is NP-Complete. The lecture uses this result without proving it.

#### Terminology

- **Literal:** a variable $x_i$ or its negation $\neg x_i$.
- **Clause:** an OR of literals, such as $(x_1\lor\neg x_2\lor x_3)$.
- **CNF (Conjunctive Normal Form):** an AND of clauses.
- **3-CNF:** a CNF formula in which **every clause contains exactly three literals** (as defined in the lecture).
- **3-SAT:** given a 3-CNF expression, decide whether a satisfying truth assignment exists.

For example,

$$
\phi=(x_1\lor x_2\lor x_3)\land(\neg x_1\lor x_3\lor x_4).
$$

A satisfying assignment needs **at least one TRUE literal in each clause**. It does **not** need every literal to be TRUE.

#### Why 3-SAT Is in NP

Given an assignment, evaluate every clause, then check whether all clauses are TRUE. The verification takes polynomial time.

#### Why 3-SAT Is NP-Complete

The lecture outlines a polynomial-time conversion from a CNF formula to an equisatisfiable 3-CNF formula, introducing auxiliary variables for clauses with more than three literals. Combining this conversion with Cook's theorem establishes 3-SAT as NP-Complete. The lecture sketches a construction using an auxiliary variable $x_A$ to replace an OR of four literals by clauses of length three, then states that the general construction follows similarly.

**Important distinction:** **3-CNF** is a *formula format*; **3-SAT** is the *decision problem* concerning formulas in that format.

### Independent Set

#### Definition

An **independent set** is a subset $V'\subseteq V$ such that **no two selected vertices are adjacent**:

$$
\forall u,v\in V',\ u\ne v:\quad (u,v)\notin E.
$$

**Decision problem:** Given a graph $G$ and integer $K$, does $G$ contain an independent set of size $K$?

#### Step 1: Independent Set Is in NP

Given a proposed vertex set $V'$:

1. Verify that it contains $K$ vertices.
2. For each pair $u,v\in V'$, verify that $(u,v)\notin E$.

There are at most $O(n^2)$ pairs. The lecture gives a polynomial bound of $O(mn^2)$ for checking edges in a general representation; using an adjacency matrix makes each pair check $O(1)$, for an $O(n^2)$ pair-check bound.

#### Step 2: Reduce 3-SAT to Independent Set

**Known NP-Complete problem:** 3-SAT.

**Target problem:** Independent Set.

Let $\phi$ contain $m$ clauses, each with three literals.

**Construction:**

1. Create **one vertex for each literal occurrence** (three per clause).
2. Connect the three vertices within each clause to form a **triangle**.
3. Connect vertices in different clauses whenever their literals are **contradictory** ($x_i$ and $\neg x_i$).
4. Set the target independent-set size to **$K=m$**, the number of clauses.

**Forward direction (3-SAT YES $\Rightarrow$ Independent Set YES):** From each satisfied clause, pick one TRUE literal. This selects $m$ vertices. No two lie in the same clause triangle, and two selected TRUE literals cannot contradict each other. Thus the chosen vertices form an independent set of size $m$.

**Reverse direction (Independent Set YES $\Rightarrow$ 3-SAT YES):** Each clause triangle contributes **at most one** vertex to an independent set. Since there are $m$ triangles and the independent set has size $m$, it contains **exactly one vertex from each clause**. The selected literals never contradict each other, so assign them TRUE and complete the other variables arbitrarily. Every clause has a TRUE literal, hence $\phi$ is satisfiable.

**Running time:** Creating $3m$ vertices and adding within-clause and contradictory-literal edges can be completed in polynomial time.

Therefore,

$$
\boxed{\text{3-SAT}\leq_p\text{Independent Set}}.
$$

Together with membership in NP, **Independent Set is NP-Complete**.

#### Fixed $K$ Versus Input $K$

The lecture also considers testing all subsets of size $K$:

$$
O(K^2n^K).
$$

For a **fixed** value such as $K=5$, this is polynomial in $n$ ($O(n^5)$). But when $K$ is part of the input, the exponent is **not fixed**. This is **not a polynomial-time algorithm in the general input size**.

### Clique

#### Definition

A **clique** is a subset $V'\subseteq V$ in which **every pair of distinct selected vertices is connected**:

$$
\forall u,v\in V',\ u\ne v:\quad (u,v)\in E.
$$

**Decision problem:** Given $G$ and integer $M$, is there a clique of size $M$?

#### Step 1: Clique Is in NP

Given $V'$, check that $|V'|=M$ and check every vertex pair for an edge. This takes polynomial time (the lecture states $O(mn^2)$; an adjacency matrix permits $O(n^2)$ pair checks).

#### Step 2: Reduce Independent Set to Clique

#### Graph Complement

Define the **complement graph** $\overline G=(V,\overline E)$, with the same vertices and exactly the edges that are **absent** from $G$ (between distinct vertices):

$$
(u,v)\in\overline E\iff(u,v)\notin E.
$$

**Construction:**

$$
\boxed{(G,K)\longmapsto(\overline G,M=K)}.
$$

**Forward:** If $V'$ is an independent set of size $K$ in $G$, every pair in $V'$ is a non-edge of $G$, so every pair becomes an edge of $\overline G$. Therefore, $V'$ is a clique of size $K$ in $\overline G$.

**Reverse:** If $V'$ is a clique of size $K$ in $\overline G$, every selected pair is an edge in $\overline G$, hence a non-edge in $G$. Therefore, $V'$ is an independent set of size $K$ in $G$.

**Running time:** Computing the graph complement takes $O(n^2)$ time.

$$
\boxed{\text{Independent Set}\leq_p\text{Clique}}.
$$

Therefore, **Clique is NP-Complete**.

### Vertex Cover

#### Definition

A **vertex cover** is a set $W\subseteq V$ such that **every edge has at least one endpoint in $W$**:

$$
\forall (u,v)\in E:\quad u\in W\ \lor\ v\in W.
$$

**Decision problem:** Given $G$ and integer $M$, does $G$ have a vertex cover of size $M$?

#### Step 1: Vertex Cover Is in NP

Given a set $W$, verify its size and inspect every edge $(u,v)$ to see whether $u\in W$ or $v\in W$. The lecture provides a polynomial bound of $O(mn)$.

#### Step 2: Reduce Independent Set to Vertex Cover

**Key lemma:** A set $V'$ is independent in $G$ **if and only if** its complement $V\setminus V'$ is a vertex cover of $G$.

Let $n=|V|$.

**Construction:** Keep the graph unchanged; change the target size:

$$
\boxed{(G,K)\longmapsto(G,M=n-K)}.
$$

**Forward:** Suppose $V'$ is independent and has size $K$. For any edge $(u,v)$, the endpoints cannot both belong to $V'$. Therefore, at least one endpoint is in $V\setminus V'$. That complement is a vertex cover of size $n-K$.

**Reverse:** Suppose $W$ is a vertex cover of size $n-K$. Every edge has an endpoint in $W$, so no edge can have both endpoints in $V\setminus W$. Thus $V\setminus W$ is an independent set of size $K$.

**Running time:** Copying the graph takes $\Theta(n+m)$ time, as stated in the lecture.

$$
\boxed{\text{Independent Set}\leq_p\text{Vertex Cover}}.
$$

Therefore, **Vertex Cover is NP-Complete**.

**Do not confuse these two transformations:**

- **Independent Set $\to$ Clique:** complement the **graph**; keep the size $K$.
- **Independent Set $\to$ Vertex Cover:** keep the **graph**; change the size to $n-K$.

### Set Cover

#### Definition

Let $U$ be a universe of elements and $\mathcal C=\{S_1,\ldots,S_n\}$ a collection of subsets of $U$. A **set cover** is a subcollection $\mathcal C'\subseteq\mathcal C$ whose union is all of $U$:

$$
\bigcup_{S\in\mathcal C'}S=U.
$$

**Decision problem:** Given $U$, the collection $\mathcal C$, and integer $K$, is there a set cover using $K$ sets?

**Example from the lecture:**

$$
U=\{1,2,3,4,5,6,7,8\}
$$

with

$$
\begin{aligned}
S_1&=\{1,2,3\}, & S_2&=\{1,4,5,8\},\\
S_3&=\{2,3,4\}, & S_4&=\{6,7\},\\
S_5&=\{2,4,6\}, & S_6&=\{2,3,5,8\}.
\end{aligned}
$$

The collection $\{S_2,S_3,S_4\}$ covers $U$. The collection $\{S_1,S_3,S_6\}$ does not, because elements $6$ and $7$ are missing.

#### Step 1: Set Cover Is in NP

Given a proposed subcollection $\mathcal C'$, check its size and compute the union of its sets, then verify that the union equals $U$. This is polynomial in the explicit input representation.

#### Step 2: Reduce Vertex Cover to Set Cover

**Known NP-Complete problem:** Vertex Cover.

**Target problem:** Set Cover.

Given $(G=(V,E),M)$, build a Set Cover instance as follows:

1. Set the **universe** to the set of graph edges: $U=E$.
2. For every vertex $v_i\in V$, create a set $S_i$ containing **all edges incident on $v_i$**:

   $$
   S_i=\{e\in E: v_i\text{ is an endpoint of }e\}.
   $$

3. Let $\mathcal C=\{S_1,\ldots,S_n\}$ and choose $K=M$.

**Forward:** If $W\subseteq V$ is a vertex cover with $M$ vertices, select the corresponding sets $S_i$ for $v_i\in W$. Every edge has an endpoint in $W$, so every edge belongs to at least one chosen set. Their union is $E=U$.

**Reverse:** If $M$ of the sets $S_i$ cover all of $U=E$, select the corresponding vertices $v_i$. Since every edge belongs to a chosen set, every edge has an endpoint among the selected vertices. They form a vertex cover of size $M$.

**Running time:** The lecture gives $O(mn)$ to construct $U$ and the collection, where $m=|E|$ and $n=|V|$.

$$
\boxed{\text{Vertex Cover}\leq_p\text{Set Cover}}.
$$

Therefore, **Set Cover is NP-Complete**.

## Reduction Summary

The reductions proved or described in the lecture form this chain:

$$
\boxed{\text{SAT}\leq_p\text{3-SAT}\leq_p\text{Independent Set}}
$$

and then branch into:

$$
\begin{aligned}
\text{Independent Set}&\leq_p\text{Clique},\\
\text{Independent Set}&\leq_p\text{Vertex Cover}\leq_p\text{Set Cover}.
\end{aligned}
$$

A separate example shows:

$$
\text{Hamiltonian Path}\leq_p\text{Decision TSP}.
$$

| Reduction | Input | Converted instance | Key idea | Lecture time bound |
| --- | --- | --- | --- | --- |
| Hamiltonian Path $\to$ TSP | $(G,v_1,v_n)$ | All edges weight $1$; $C=n$ | A Hamiltonian path uses $n-1$ edges | Polynomial |
| 3-SAT $\to$ Independent Set | $m$ clauses | Literal-occurrence graph; $K=m$ | One consistent literal per clause | Polynomial |
| Independent Set $\to$ Clique | $(G,K)$ | $(\overline G,K)$ | Non-edges become edges | $O(n^2)$ |
| Independent Set $\to$ Vertex Cover | $(G,K)$ | $(G,n-K)$ | Complement of selected vertices | $\Theta(n+m)$ |
| Vertex Cover $\to$ Set Cover | $(G,M)$ | $U=E$, one incident-edge set per vertex, $K=M$ | Cover edges by selecting vertices/sets | $O(mn)$ |

## General Proof Template

The lecture concludes with the following **five-step reduction proof structure**.

**Goal:** Prove $Q_1\leq_p Q_2$.

### Step 1: Define an instance of $Q_1$

Let $q_1$ be an arbitrary instance of the **known problem** $Q_1$. State all its input objects and parameters.

### Step 2: Construct an instance of $Q_2$

Describe how to transform $q_1$ into an instance $q_2$ of the **target problem** $Q_2$. Specify what happens to every important input object and target parameter.

### Step 3: Prove the YES-if-and-only-if-YES statement

Prove both directions:

$$
\boxed{q_1\text{ is YES}\iff q_2\text{ is YES}}.
$$

- **Forward:** Assume $q_1$ is YES. Construct or identify a valid solution for $q_2$.
- **Reverse:** Assume $q_2$ is YES. Construct or identify a valid solution for $q_1$.

This is usually the most important part of the proof.

### Step 4: State the mapping explicitly

Write the conversion in mathematical form, e.g.

$$
(G,K)\longmapsto(\overline G,K).
$$

### Step 5: Show polynomial running time

Bound the time required to **construct the new instance**, not the time required to solve the target problem. For example, generating a complement graph takes $O(n^2)$.

### Applying the Template: Independent Set to Clique

1. **Source instance:** $(G,K)$ of Independent Set.
2. **Construction:** Form $\overline G$ and use $(\overline G,K)$ for Clique.
3. **Both directions:** A chosen set has no internal edges in $G$ **if and only if** it has all internal edges in $\overline G$.
4. **Mapping:** $(G,K)\mapsto(\overline G,K)$.
5. **Time:** $O(n^2)$.

Since Clique is also in NP and Independent Set is already NP-Complete, this proves Clique is NP-Complete.

## Examples

These worked examples follow the lecture's emphasis on writing down the **certificate**, **conversion**, **YES iff YES proof**, and **polynomial construction time**.

### Proving a Problem is in NP

**Question:** Show that **Vertex Cover** is in NP.

**Step 1 — Certificate.** A subset $W\subseteq V$ claimed to contain $M$ vertices and cover all edges.

**Step 2 — Check the size.** Verify $|W|=M$ and that the selected objects are distinct vertices of $G$.

**Step 3 — Check the condition.** For every $(u,v)\in E$, verify that $u\in W$ **or** $v\in W$.

**Step 4 — Time complexity.** There are $m$ edges. With a straightforward $O(n)$ membership check per endpoint, verification takes $O(mn)$, which is polynomial.

**Conclusion:**

$$
\boxed{\text{Vertex Cover}\in NP}.
$$

### Constructing a Reduction

**Example from the lecture — Hamiltonian Path $\leq_p$ Decision TSP.**

**Input to Hamiltonian Path:** $(G,v_1,v_n)$, with $n=|V|$.

**Construction:** Give every existing edge of $G$ weight $1$, obtaining $G'$, and choose decision-TSP bound $C=n$.

**Correctness:** Any path visiting all $n$ vertices exactly once uses $n-1$ edges, hence has weight $n-1<n$. Conversely, any valid TSP path with weight less than $n$ visits all vertices exactly once and uses the original edges, so it is a Hamiltonian path in $G$.

Therefore,

$$
\text{Hamiltonian Path} \leq_p \text{Decision TSP}.
$$

**Why $C=n$?** A path visiting all $n$ vertices exactly once has **$n-1$ edges**. Giving every existing edge weight $1$ makes its total weight $n-1$, which is **strictly less** than $n$.

This example illustrates the difference between **changing the instance** and **solving the instance**: we assign weights and a bound; we do not calculate the minimum path ourselves.

### Proving NP-Completeness

**Question:** Prove that **Clique** is NP-Complete, using Independent Set as the known NP-Complete problem.

**Part A — Show Clique is in NP.** Given $S\subseteq V$ of size $K$, inspect every selected vertex pair and confirm that each pair has an edge. Using an adjacency matrix, this takes $O(n^2)$ time.

**Part B — Reduce Independent Set to Clique.**

1. **Source instance:** $(G,K)$ of Independent Set.
2. **Target instance:** $(\overline G,K)$ of Clique, where $\overline G$ complements the edges between distinct vertices.
3. **Forward ($\Rightarrow$):** If $S$ has no internal edges in $G$, all its vertex pairs are edges in $\overline G$, so $S$ is a clique.
4. **Reverse ($\Leftarrow$):** If $S$ is a clique in $\overline G$, none of its vertex pairs is an edge in $G$, so $S$ is independent.
5. **Time:** Constructing $\overline G$ from an adjacency matrix takes $O(n^2)$.

Therefore,

$$
\text{Independent Set}\leq_p\text{Clique}.
$$

Since Independent Set is NP-Complete, Clique is NP-Hard. Because Clique is also in NP,

$$
\boxed{\text{Clique is NP-Complete}}.
$$

**Exam reminder:** The reduction direction is **Independent Set $\to$ Clique**, not Clique $\to$ Independent Set for this particular proof (even though a different converse reduction also exists).

## Key Takeaways

- **P** means polynomial-time **solvable**; **NP** means polynomial-time **verifiable**.
- **NP-Complete** requires **both** membership in NP and NP-Hardness.
- To prove a new problem NP-Complete, reduce a **known NP-Complete problem into it**, not the other way around.
- A correct reduction preserves answers in **both directions**: YES $\iff$ YES.
- The conversion must itself be **polynomial-time**.
- **Independent Set:** selected vertices have **no edges between them**.
- **Clique:** selected vertices have **all possible edges between them**.
- **Vertex Cover:** selected vertices touch **every edge**.
- **Set Cover:** selected sets contain **every element of the universe**.
- A runtime like $O(n^K)$ is polynomial only if $K$ is **fixed**, not when $K$ is part of the input.

---

*Source: CSE 2331 — NP Completeness lecture notes, pp. 1–23. This note follows the lecture's definitions and reduction sequence; explanatory notation and a few expanded verification details are added for readability.*

---

*Source: CSE 2331 — NP Completeness lecture notes, pp. 1–23. Explanatory worked examples and proof checklists are added for studying; the lecture's problem statements and reduction directions are retained.*

  - Clique
    - Graph Complement
  - Vertex Cover
  - Set Cover
- Reduction Summary
- General Proof Template
- Examples
  - Proving a Problem is in NP
  - Constructing a Reduction
  - Proving NP-Completeness
- Key Takeaways
