# Computational Complexity: A 2-Hour Introduction

## Lecture Overview

**Audience:** Graduate students from diverse backgrounds (CS, physics, law, bioinformatics, IT)  
**Prerequisite assumed:** Freshman-level algebra, informal notion of "algorithm"  
**Duration:** ~120 minutes (with a 10-minute break)  
**Goal:** Build intuition for *why* some problems are fundamentally harder than others, culminating in the Cook–Levin theorem, Karp reductions, and 3-SAT.

---

## Part 0 — Motivation and Framing (10 min)

### The Central Question

> "We can all agree that some tasks feel harder than others. But can we make that precise? Can we *prove* that a problem is hard — not just that we haven't been clever enough yet?"

### Relatable examples for diverse backgrounds

Ask each student to think about a problem from their own field where finding an answer is hard but *checking* an answer is easy:

- **Lawyer:** Given a complex contract, is there any interpretation of the clauses that creates a contradiction? Hard to find, but if someone hands you the specific interpretation, you can verify it.
- **Bioinformatician:** Given a protein, does any 3D fold minimize energy below a threshold? Enormous search space, but a proposed fold can be scored.
- **Physicist:** Given a spin-glass Hamiltonian, what is the ground-state energy? Exponential configurations, but any given configuration can be evaluated.
- **IT professional:** Given a network of servers with capacity constraints, can you route all traffic without exceeding any link's capacity? Hard to plan, easy to audit.

### The punchline (preview)

Complexity theory formalizes this asymmetry between *finding* and *verifying*. That asymmetry is the heart of the $P$ vs. $NP$ question — arguably the most important open problem in all of mathematics and computer science.

---

## Part 1 — Problems, Instances, and Algorithms (15 min)

### What is a "problem"?

A **decision problem** asks a yes/no question parameterized by an input.

- **Input:** An *instance* — some data encoded as a string of symbols.
- **Output:** YES or NO.

**Example — DIVISIBILITY:**  
*Input:* Two positive integers $a$ and $b$.  
*Question:* Does $b$ divide $a$ evenly (i.e., is $a \mod b = 0$)?

### Instance size

We measure the "difficulty" of an instance by its **size**, denoted $n$. This is typically the number of bits (or symbols) needed to write down the input.

**Key point for non-CS students:** The number $1{,}000{,}000$ looks like it has seven digits, but in binary it has about 20 bits. Size is about how much space the input takes to write down, not the magnitude of the numbers involved.

### Algorithms and running time

An **algorithm** is a step-by-step procedure that halts and produces an answer for every valid input.

The **running time** of an algorithm is a function $T(n)$ that counts the number of elementary steps on an input of size $n$.

### Big-$O$ notation (quick refresher)

We write $T(n) = O(f(n))$ to mean: there exist constants $c > 0$ and $n_0$ such that for all $n \geq n_0$,

$$T(n) \leq c \cdot f(n)$$

Intuitively: $T$ grows *no faster than* $f$, ignoring constant factors.

| Notation | Name | Example task |
|----------|------|-------------|
| $O(1)$ | Constant | Look up an item by index |
| $O(\log n)$ | Logarithmic | Binary search |
| $O(n)$ | Linear | Scan a list once |
| $O(n \log n)$ | Log-linear | Sort a list (merge sort) |
| $O(n^2)$ | Quadratic | Compare all pairs |
| $O(2^n)$ | Exponential | Try all subsets |

### Polynomial vs. exponential: why it matters

| $n$ | $n^2$ | $n^3$ | $2^n$ |
|-----|-------|-------|-------|
| 10 | 100 | 1,000 | 1,024 |
| 20 | 400 | 8,000 | 1,048,576 |
| 50 | 2,500 | 125,000 | $\approx 10^{15}$ |
| 100 | 10,000 | 1,000,000 | $\approx 10^{30}$ |

At $n = 100$, a polynomial-time algorithm finishes in under a second on a modern computer. An exponential-time algorithm would take longer than the age of the universe.

**Takeaway:** Polynomial time ($O(n^k)$ for some fixed $k$) is our formal stand-in for "feasible." Exponential time means "practically impossible for large inputs."

---

## Part 2 — The Class $P$ (10 min)

### Definition

$$P = \{\text{decision problems solvable by a deterministic algorithm in polynomial time}\}$$

A problem is in $P$ if there exists an algorithm that, for every input of size $n$, produces the correct YES/NO answer in at most $O(n^k)$ steps for some fixed constant $k$.

### Examples of problems in $P$

1. **SORTING** (decision version): "Given a list, is the $i$-th smallest element equal to $x$?"  
   Solvable in $O(n \log n)$.

2. **SHORTEST PATH:** "Given a weighted graph, is there a path from $s$ to $t$ of total weight $\leq W$?"  
   Dijkstra's algorithm runs in $O(n^2)$ (or better with heaps).

3. **LINEAR PROGRAMMING:** "Given a system of linear inequalities, is there a feasible point?"  
   Solvable in polynomial time (Khachiyan 1979, Karmarkar 1984).

4. **PRIMALITY TESTING:** "Given an integer $n$, is $n$ prime?"  
   AKS algorithm (2002): $O((\log n)^{6})$ — polynomial in the *size* of the input (number of digits).

### Why $P$ matters

$P$ represents the class of problems we consider **tractable** — efficiently solvable by computers. If your problem is in $P$, you can (in principle) solve it on real-world inputs.

---

## Part 3 — The Class $NP$ (15 min)

### The verification intuition

Think of $NP$ not in terms of *solving* but in terms of *checking*.

**Analogy:** A Sudoku puzzle.
- **Solving** it might take you a long time (you try possibilities, backtrack, reason through constraints).
- **Checking** a completed grid takes only a minute: verify each row, column, and box has the digits 1–9 with no repeats.

### Certificates (witnesses)

A **certificate** (or **witness**) is a piece of evidence that a YES answer is correct.

For a YES instance, *someone* hands you a certificate. You then **verify** it in polynomial time.

### Formal definition

A decision problem $L$ is in $NP$ if there exists a polynomial-time **verifier** $V$ such that:

- **Completeness:** If $x$ is a YES instance, then there exists a certificate $c$ (of size polynomial in $|x|$) such that $V(x, c) = \text{YES}$.
- **Soundness:** If $x$ is a NO instance, then for *every* string $c$, $V(x, c) = \text{NO}$.

### Unpacking "nondeterministic"

The "$N$" in $NP$ stands for **nondeterministic**. The original definition uses a hypothetical "nondeterministic Turing machine" that can magically guess the right certificate. But the verifier-based definition above is equivalent and more intuitive:

$$NP = \{\text{decision problems whose YES instances have polynomial-time verifiable certificates}\}$$

### Critical observation: $P \subseteq NP$

Every problem in $P$ is also in $NP$. Why? If you can *solve* a problem in polynomial time, you can certainly *verify* a YES answer in polynomial time — just solve it from scratch and compare. The certificate can even be empty.

### Examples of problems in $NP$

1. **SUBSET-SUM:** "Given a set $S$ of integers and a target $t$, is there a subset of $S$ that sums to exactly $t$?"
   - Certificate: the subset itself.
   - Verification: add up the elements, check if the sum equals $t$. Takes $O(n)$ time.

2. **HAMILTONIAN PATH:** "Given a graph $G$, is there a path that visits every vertex exactly once?"
   - Certificate: the sequence of vertices.
   - Verification: check that it's a valid path in $G$ and visits every vertex. Takes $O(n^2)$ time.

3. **SATISFIABILITY (SAT):** (We'll spend a lot of time on this one.)
   - Certificate: a truth assignment.
   - Verification: plug in the values and evaluate the formula.

### The big question

$$P \stackrel{?}{=} NP$$

- If $P = NP$: every problem whose solutions are easy to *check* is also easy to *solve*. This would be revolutionary (and devastating — most of modern cryptography would collapse).
- If $P \neq NP$: there exist problems where checking is fundamentally easier than solving.

**Current status:** Most researchers believe $P \neq NP$, but *no one has proved it*. The Clay Mathematics Institute offers a \$1,000,000 prize for a proof either way.

---

## Part 4 — Boolean Satisfiability (SAT) (15 min)

### Boolean variables and formulas

A **Boolean variable** takes one of two values: TRUE ($1$) or FALSE ($0$).

A **Boolean formula** is built from:
- Variables: $x_1, x_2, x_3, \ldots$
- NOT (negation): $\neg x$ (flips the value)
- AND (conjunction): $x \wedge y$ (true only if both are true)
- OR (disjunction): $x \vee y$ (true if at least one is true)

**Truth table refresher:**

| $x$ | $y$ | $\neg x$ | $x \wedge y$ | $x \vee y$ |
|-----|-----|----------|-------------|------------|
| 0 | 0 | 1 | 0 | 0 |
| 0 | 1 | 1 | 0 | 1 |
| 1 | 0 | 0 | 0 | 1 |
| 1 | 1 | 0 | 1 | 1 |

### The SAT problem

**SAT (Boolean Satisfiability):**  
*Input:* A Boolean formula $\varphi$ over variables $x_1, \ldots, x_n$.  
*Question:* Is there an assignment of TRUE/FALSE to each variable that makes $\varphi$ evaluate to TRUE?

**Example:**  
$\varphi = (x_1 \vee x_2) \wedge (\neg x_1 \vee x_3) \wedge (\neg x_2 \vee \neg x_3)$

Try $x_1 = 1, x_2 = 0, x_3 = 1$:
- Clause 1: $1 \vee 0 = 1$ ✓
- Clause 2: $0 \vee 1 = 1$ ✓
- Clause 3: $1 \vee 0 = 1$ ✓

So $\varphi$ is satisfiable. The assignment $(1, 0, 1)$ is a **satisfying assignment** (certificate).

### Conjunctive Normal Form (CNF)

A formula is in **CNF** if it is an AND of ORs:

$$\varphi = C_1 \wedge C_2 \wedge \cdots \wedge C_m$$

Each $C_i$ is a **clause** — an OR of **literals** (a literal is a variable $x_j$ or its negation $\neg x_j$).

**Example:** $\varphi = (x_1 \vee \neg x_2) \wedge (\neg x_1 \vee x_2 \vee x_3)$

### $k$-SAT

**$k$-SAT** is SAT restricted to CNF formulas where every clause has exactly $k$ literals.

- **2-SAT:** Each clause has exactly 2 literals.  
  Example: $(x_1 \vee \neg x_2) \wedge (\neg x_1 \vee x_3) \wedge (x_2 \vee \neg x_3)$

- **3-SAT:** Each clause has exactly 3 literals.  
  Example: $(x_1 \vee \neg x_2 \vee x_3) \wedge (\neg x_1 \vee x_2 \vee \neg x_4)$

**Crucial fact:** 2-SAT is in $P$ (solvable in linear time via implication graphs). But 3-SAT is $NP$-complete — as hard as anything in $NP$.

### Why SAT is natural

SAT captures a universal pattern: you have a bunch of constraints (clauses), each involving a few variables, and you want to know if all constraints can be simultaneously satisfied. This pattern appears in:

- **Circuit design** (do these logic gates produce the right output?)
- **Scheduling** (can all these requirements be met?)
- **Verification** (does this software satisfy its specification?)
- **Bioinformatics** (is this gene expression pattern consistent with the regulatory network?)

---

## ☕ Break (10 min)

---

## Part 5 — $NP$-Completeness and Reductions (20 min)

### The idea of reduction

A **reduction** from problem $A$ to problem $B$ is a transformation:

$$\text{Instance of } A \xrightarrow{\text{polynomial-time}} \text{Instance of } B$$

such that the answer to the $A$-instance is YES *if and only if* the answer to the transformed $B$-instance is YES.

**Intuition:** "$A$ reduces to $B$" means "$A$ is no harder than $B$." If you can solve $B$, you can solve $A$ by first transforming and then solving $B$.

**Notation:** $A \leq_p B$ (read "$A$ is polynomial-time reducible to $B$").

### A simple, concrete reduction example

**Problem ELEMENT-IN-LIST:**  
*Input:* A list $L$ of integers and a target integer $t$.  
*Question:* Is $t$ in the list $L$?

**Problem LIST-HAS-ZERO:**  
*Input:* A list $L'$ of integers.  
*Question:* Does $L'$ contain the integer $0$?

**Claim:** ELEMENT-IN-LIST $\leq_p$ LIST-HAS-ZERO.

**Reduction:** Given an instance $(L, t)$ of ELEMENT-IN-LIST, construct a new list $L'$ by subtracting $t$ from every element of $L$:

$$L' = [a_1 - t,\ a_2 - t,\ \ldots,\ a_n - t]$$

Now:
- If $t \in L$, then some $a_i = t$, so $a_i - t = 0$, and $L'$ contains $0$. → LIST-HAS-ZERO answers YES.
- If $t \notin L$, then every $a_i \neq t$, so every $a_i - t \neq 0$, and $L'$ does not contain $0$. → LIST-HAS-ZERO answers NO.

This transformation takes $O(n)$ time (one subtraction per element), so it's polynomial. Therefore, if you have a solver for LIST-HAS-ZERO, you can solve ELEMENT-IN-LIST by first applying this transformation.

**The key properties of any valid reduction:**
1. The transformation runs in **polynomial time**.
2. YES maps to YES, and NO maps to NO (equivalence is preserved in both directions).

### Karp reductions (many-one reductions)

The type of reduction just described — transform the instance, pass it to the other problem's solver, and return that answer directly — is called a **Karp reduction** (or **many-one polynomial-time reduction**), named after Richard Karp.

Formally: $A \leq_p B$ via Karp reduction if there exists a polynomial-time computable function $f$ such that:

$$x \in A \iff f(x) \in B$$

### $NP$-Hardness and $NP$-Completeness

**Definition:** A problem $B$ is **$NP$-hard** if *every* problem in $NP$ can be polynomial-time reduced to $B$:

$$\forall A \in NP: \quad A \leq_p B$$

This means $B$ is "at least as hard as everything in $NP$."

**Definition:** A problem $B$ is **$NP$-complete** if:
1. $B \in NP$ (it's in $NP$ — solutions can be verified in polynomial time), AND
2. $B$ is $NP$-hard (everything in $NP$ reduces to it).

**Why this matters:** If any single $NP$-complete problem can be solved in polynomial time, then $P = NP$ (because every problem in $NP$ reduces to it). Conversely, if $P \neq NP$, then no $NP$-complete problem has a polynomial-time algorithm.

$NP$-complete problems are the "hardest" problems in $NP$. They sit at the boundary.

### Building a chain of reductions

Once you know one problem is $NP$-complete, you can prove others are $NP$-complete by reduction:

To show that problem $X$ is $NP$-complete:
1. Show $X \in NP$ (exhibit a polynomial-time verifier).
2. Take a known $NP$-complete problem $Y$ and show $Y \leq_p X$.

Why does step 2 work? Because every problem in $NP$ reduces to $Y$ (by $Y$'s $NP$-completeness), and $Y$ reduces to $X$. Reductions compose: if $A \leq_p Y$ and $Y \leq_p X$, then $A \leq_p X$. So every problem in $NP$ reduces to $X$, making $X$ $NP$-hard.

This gives a powerful methodology: prove one foundational problem $NP$-complete, then build a cascade.

---

## Part 6 — The Cook–Levin Theorem (15 min)

### The foundational result

**Theorem (Cook 1971, Levin 1973):** SAT is $NP$-complete.

This was proven independently by Stephen Cook (in North America) and Leonid Levin (in the Soviet Union). It is the "first" $NP$-completeness result — the foundation on which all others rest.

### Why is this surprising?

We need to show that *every* problem in $NP$ — scheduling, graph problems, protein folding, logic puzzles, *everything* — reduces to SAT. That's an extraordinarily strong claim.

### Proof intuition (not a formal proof)

The key insight: any $NP$ problem has a polynomial-time verifier (that's what it means to be in $NP$). A verifier is a computation. Any computation can be modeled as a Boolean circuit. A Boolean circuit can be turned into a SAT formula.

Here is the argument in more detail:

**Step 1: Any NP problem has a verifier.**  
If $L \in NP$, there is a verifier $V$ that, given input $x$ and certificate $c$, runs in time $p(|x|)$ for some polynomial $p$, and $V(x,c) = 1$ iff $x \in L$.

**Step 2: The verifier is a computation, and any computation can be described as a circuit.**  
Model $V$ as a Boolean circuit of size polynomial in $|x|$. The circuit takes bits of $x$ (known/fixed) and bits of $c$ (unknown) as inputs, and outputs 1 iff $V$ accepts.

**Step 3: "Does this circuit output 1 for some input $c$?" is a SAT instance.**  
Hardwire the known bits of $x$ into the circuit. The remaining free inputs are the bits of $c$. Introduce a Boolean variable for each gate's output. Write a clause for each gate capturing its truth table. The conjunction of all these clauses is satisfiable *if and only if* there exists a certificate $c$ that makes $V$ accept.

**Result:** We have a polynomial-time transformation from any instance $x$ of any $NP$ problem $L$ into a SAT formula $\varphi_x$ that is satisfiable iff $x \in L$.

### What Cook–Levin gives us

A single $NP$-complete problem: SAT. From here, we can prove other problems $NP$-complete by reducing SAT (or any already-known $NP$-complete problem) to them.

---

## Part 7 — 3-SAT and Karp's 21 Problems (15 min)

### From SAT to 3-SAT

**Theorem:** 3-SAT is $NP$-complete.

**Proof sketch:** We show SAT $\leq_p$ 3-SAT. Given a SAT formula, we transform each clause into an equivalent set of 3-literal clauses.

**Case 1: Clause has 1 literal** — say $(a)$.  
Introduce two new variables $y_1, y_2$ and replace with:
$$(a \vee y_1 \vee y_2) \wedge (a \vee y_1 \vee \neg y_2) \wedge (a \vee \neg y_1 \vee y_2) \wedge (a \vee \neg y_1 \vee \neg y_2)$$
These four clauses are simultaneously satisfiable iff $a = 1$ (you can check: if $a = 0$, the four clauses enumerate all combinations of $y_1, y_2$, making it impossible to satisfy all four).

**Case 2: Clause has 2 literals** — say $(a \vee b)$.  
Introduce one new variable $y$ and replace with:
$$(a \vee b \vee y) \wedge (a \vee b \vee \neg y)$$
Satisfiable iff $a \vee b = 1$.

**Case 3: Clause has exactly 3 literals** — keep as is.

**Case 4: Clause has $k > 3$ literals** — say $(a_1 \vee a_2 \vee \cdots \vee a_k)$.  
Introduce new variables $y_1, y_2, \ldots, y_{k-3}$ and replace with the chain:

$$(a_1 \vee a_2 \vee y_1)$$
$$(\neg y_1 \vee a_3 \vee y_2)$$
$$(\neg y_2 \vee a_4 \vee y_3)$$
$$\vdots$$
$$(\neg y_{k-3} \vee a_{k-1} \vee a_k)$$

The new variables act as "carry" bits that propagate the OR along the chain. This set of 3-literal clauses is satisfiable iff the original long clause is satisfiable.

**Why this matters:** 3-SAT is a "cleaner" starting point for reductions than general SAT, because every clause has a uniform structure.

### Karp's 21 $NP$-complete problems (1972)

One year after Cook's theorem, Richard Karp published a landmark paper showing that 21 important problems are all $NP$-complete, by building a chain of reductions from SAT.

Here is a selection (we won't prove all of these, but they illustrate the breadth):

| Problem | Informal description | Field |
|---------|---------------------|-------|
| 3-SAT | Satisfiability with 3-literal clauses | Logic |
| VERTEX COVER | Fewest vertices to "cover" all edges | Graph theory |
| SET COVER | Fewest subsets to cover a universe | Combinatorics |
| SUBSET-SUM | Subset summing to a target | Number theory |
| HAMILTONIAN PATH | Path visiting every vertex once | Graph theory |
| GRAPH COLORING | Color vertices with $k$ colors, no adjacent same | Graph theory |
| INTEGER PROGRAMMING | Linear program with integer variables | Optimization |
| KNAPSACK (decision) | Pack items to exceed value within weight limit | Optimization |

**The cascade:** SAT $\to$ 3-SAT $\to$ VERTEX COVER $\to$ SET COVER, and so on. Each arrow is a polynomial-time reduction.

### A visual picture

Think of $NP$-complete problems as a "club" — once you're in (by being reduced to from a known member), you can serve as the basis for inducting new members. The Cook–Levin theorem provides the first member (SAT). Karp's paper opened the floodgates.

Today, *thousands* of problems are known to be $NP$-complete. Whenever you encounter a new problem and suspect it's hard, the standard approach is:

1. Show it's in $NP$ (find a polynomial-time verifier).
2. Reduce a known $NP$-complete problem to it.

---

## Part 8 — What Does $NP$-Completeness Mean in Practice? (10 min)

### For the IT professional

If your optimization problem is $NP$-complete (or its optimization version is $NP$-hard), don't expect to find a perfect solution for large instances. Instead, consider:

- **Heuristics and approximation algorithms:** Find a "good enough" solution with provable guarantees.
- **Special cases:** Your real-world instances might have structure that makes them easier (e.g., planar graphs, bounded treewidth).
- **Parameterized complexity:** If a parameter $k$ is small (e.g., number of colors), algorithms exponential only in $k$ might be practical.
- **SAT solvers:** Modern SAT solvers can handle instances with *millions* of variables in practice, despite worst-case exponential time.

### For the lawyer

$NP$-completeness results have implications for computational approaches to legal reasoning. If checking whether a set of legal rules is consistent can be modeled as SAT (and it often can), then the general problem of finding consistent interpretations is $NP$-complete. This doesn't mean it's hopeless — but it means there's no universal efficient algorithm.

### For the bioinformatician

Many problems in bioinformatics are $NP$-hard: sequence alignment with complex scoring, protein structure prediction, phylogenetic tree reconstruction under certain models. Knowing this helps you choose the right algorithmic strategy (approximation, heuristic, or exact with pruning).

### For the physicist

The connection between $NP$-completeness and statistical mechanics is deep. Finding ground states of spin glasses is $NP$-hard. This is one reason why physicists developed simulated annealing — a heuristic inspired by physical cooling that approximately solves $NP$-hard optimization problems.

---

## Part 9 — The Bigger Picture and Open Questions (10 min)

### The complexity zoo (a taste)

$P$ and $NP$ are just two of many complexity classes:

- $\text{co-}NP$: Problems whose NO instances have short certificates. Example: "Is this formula *unsatisfiable*?"
- $NP \cap \text{co-}NP$: Problems where both YES and NO instances have short proofs. Primality testing turned out to be here (and even in $P$!).
- $PSPACE$: Problems solvable with polynomial *memory* (but potentially exponential *time*). Example: determining the winner of a two-player game like generalized chess.
- $EXP$: Problems solvable in exponential time.

The known containment hierarchy:

$$P \subseteq NP \subseteq PSPACE \subseteq EXP$$

Whether any of these containments is strict (except $P \subsetneq EXP$, which is known) remains open.

### Beyond decision problems

Many real-world problems are **optimization** problems ("find the *best* solution"), not just decision problems. The decision version ("does a solution of quality $\geq k$ exist?") is typically the one classified as $NP$-complete. But if the decision version is $NP$-complete, the optimization version is $NP$-hard, meaning it's at least as hard.

### Philosophical implications of $P$ vs. $NP$

If $P = NP$:
- Creativity and discovery could (in principle) be automated: finding a proof would be as easy as verifying one.
- Cryptography as we know it would collapse — breaking codes would be as easy as making them.
- Mathematical proof verification (already in $NP$) would become polynomial-time proof *discovery*.

If $P \neq NP$:
- There exist problems with an inherent, provable gap between finding and verifying.
- Cryptography has a theoretical foundation.
- Some computational problems are *fundamentally* beyond efficient solution.

---

## Summary and Key Takeaways

| Concept | One-sentence summary |
|---------|---------------------|
| $P$ | Problems solvable efficiently (polynomial time). |
| $NP$ | Problems whose YES answers are *verifiable* efficiently. |
| $P \subseteq NP$ | Solving is at least as hard as verifying. |
| $P \stackrel{?}{=} NP$ | The biggest open question: is verifying strictly easier than solving? |
| Reduction ($A \leq_p B$) | A polynomial-time transformation showing $A$ is no harder than $B$. |
| $NP$-hard | At least as hard as everything in $NP$. |
| $NP$-complete | In $NP$ *and* $NP$-hard — the hardest problems in $NP$. |
| Cook–Levin | SAT is $NP$-complete (the foundational result). |
| 3-SAT | SAT with 3-literal clauses; also $NP$-complete. |
| Karp reductions | The standard tool for proving $NP$-completeness by chain. |

---

## Appendix A — Worked Example: Proving SUBSET-SUM is $NP$-complete

This is a sketch of the reduction 3-SAT $\leq_p$ SUBSET-SUM (for students wanting more depth).

**Step 1:** SUBSET-SUM $\in NP$.  
Certificate: the subset. Verification: sum the elements, compare to target. Polynomial time. ✓

**Step 2:** 3-SAT $\leq_p$ SUBSET-SUM (sketch).

Given a 3-SAT formula $\varphi$ with $n$ variables $x_1, \ldots, x_n$ and $m$ clauses $C_1, \ldots, C_m$:

- Construct $2n + 2m$ numbers (in a carefully chosen base, typically base 10 is fine if digits don't carry).
- For each variable $x_i$: create two numbers, one for $x_i = \text{TRUE}$ and one for $x_i = \text{FALSE}$.
- Encode which clauses each literal satisfies using digit positions.
- Add "slack" numbers to allow each clause column to hit an exact target.
- Set the target number so that exactly one of $\{x_i, \neg x_i\}$ is chosen per variable, and every clause is satisfied.

The construction is polynomial in $n + m$, and the SUBSET-SUM instance is YES iff $\varphi$ is satisfiable.

---

## Appendix B — Discussion Questions for Class

1. Your friend claims they wrote a polynomial-time algorithm for 3-SAT. What would the implications be? What is your first reaction?

2. We said 2-SAT is in $P$ but 3-SAT is $NP$-complete. Why does adding just one literal per clause make such a dramatic difference? (Hint: think about the structure of implications in 2-SAT.)

3. A bioinformatics company advertises that their software "solves" an $NP$-hard protein folding problem. Is this a contradiction? What might they actually mean?

4. If you could prove that $P = NP$, would you publish the proof? What are the societal consequences?

5. Find a problem from your own field that you suspect is $NP$-hard. What is the decision version? What would a certificate look like?

---

## Appendix C — Recommended Reading

- **Sipser, M.** *Introduction to the Theory of Computation* — Chapter 7 (accessible, rigorous).
- **Arora, S. & Barak, B.** *Computational Complexity: A Modern Approach* — Chapters 2–3 (graduate-level depth).
- **Garey, M.R. & Johnson, D.S.** *Computers and Intractability* — The classic reference on $NP$-completeness, with an extensive catalog of problems.
- **Wigderson, A.** *Mathematics and Computation* — Beautiful exposition of the philosophical implications; freely available online.
- **Fortnow, L.** *The Golden Ticket: P, NP, and the Search for the Impossible* — A popular-science account, excellent for non-CS backgrounds.
