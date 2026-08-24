# Conversation
- conversationId: fac60129-3ff4-50c9-aabf-4981c8c63da5
- endpoint: anthropic
- title: Day 1 Math & Rust Worksheet
- exportAt: 02:06:58 GMT+0530 (India Standard Time)

## Options
- presetId: null
- model: claude-opus-5
- resendFiles: true
- maxContextTokens: 828400
- endpoint: anthropic
- endpointType: undefined
- title: Day 1 Math & Rust Worksheet

## History
**User**
Act as an IIT professor and Senior CSE Instructor. Create a highly structured Daily Study Worksheet for Day 1 of my curriculum. I need exactly 2 sections 1. 1) Advanced Engineering Mathematics 2. A short code execution drill in Rust focused on memory ownership. Provide hints and detailed analytical breakdowns for each solution.


**Claude**
<details>
<summary>Thinking</summary>

I'm structuring a Day 1 worksheet with two sections—advanced engineering mathematics and a Rust ownership drill—adopting the voice of an IIT professor/senior CSE instructor with time allocations, learning objectives, progressive hints, and full analytical solutions. Now I'm settling on a coherent theme for the math section.

For the linear algebra piece, I'm designing a classic parameterized system problem to test rank and consistency analysis, using row reduction to determine when the system has a unique solution, infinitely many solutions, or no solution based on the parameters λ and μ, verifying each case carefully before finalizing the problem set.

Next I'm setting up an eigenvalue problem with a symmetric matrix whose eigenvalues are 4, 1, 1, planning to have students find eigenvectors, verify diagonalizability, and apply Cayley-Hamilton to compute the inverse and higher powers of the matrix, and I'm now working out the characteristic polynomial.

Using the trick that this matrix equals the identity plus an all-ones matrix simplifies things nicely, since the all-ones matrix squares to three times itself. This lets me express A² compactly and then derive A⁻¹ = (1/4)(4I - J) directly from Cayley-Hamilton, which gives a clean check for the eventual solution.

Now considering the third problem: since the theme is linear algebra, I'm thinking of tying it together with a system of ODEs using this same matrix A, so solving dX/dt = AX would connect naturally to the earlier work.

Working out the solution using e^{At} = e^t·e^{Jt}, and since I and J commute, I expand e^{Jt} as a series that resolves into I + J·((e^{3t}-1)/3), giving a clean closed-form solution for X(t) with the given initial condition.

For the next problem, I'm considering a proof-based question on rank inequalities, like showing rank(AB) ≤ min(rank A, rank B).

Alternatively, tying back to the symmetric matrix from A2, I could ask to prove that a real symmetric matrix has real eigenvalues and that eigenvectors for distinct eigenvalues are orthogonal — that's a nice theoretical complement.

For a final quick problem, I'm thinking of a quadratic form question: since A has eigenvalues 4, 1, 1 (all positive), the form is positive definite, so I could ask for the minimum of Q(x) = x^T A x on the unit sphere.

Now moving to section 2, I want a Rust ownership drill with a handful of short code snippets where the reader predicts output, decides if it compiles, or fixes a bug. The focus areas are move semantics, borrow checker rules, the Copy trait, cloning, mutable borrow conflicts, drop order, non-lexical lifetimes, and vector/slice reallocation versus String and &str behavior. I'm sketching out the individual drills — one testing a use-after-move error, one contrasting Copy types like i32 with non-Copy types, one exploring overlapping immutable/mutable borrows where NLL changes the outcome, and one involving pushing to a vector while holding a reference.

I'm also planning drills on drop order and ownership transfer into functions, plus a harder one involving lifetime errors or clone/shadowing interactions. I want to include hints, exact error codes, a rules table, self-assessment rubric, timing guidance, a Day-2 preview, common pitfalls, and some viva-style questions to round it out. Writing drill code...

Output is 9 9, and uncommenting the borrow after the move fails as expected.

Next I'm designing a borrow-rules drill showing E0502: taking a reference to a vector element, then pushing (which may reallocate and invalidate the reference), causing a dangling pointer error—while noting that reordering the print before the push compiles fine thanks to NLL. Then I move to a mutable aliasing example with two shared references before introducing a mutable one.

I trace through: two immutable borrows print fine together, then a mutable borrow appends to the string and prints the updated value—all valid under NLL since the borrows don't overlap. Next I build a drop-order drill using a Noisy struct with a custom Drop impl, tracing how consuming one instance early, then declaring another locally, produces drops in reverse declaration order at scope end (last declared drops first), landing on the sequence: consume B, drop B, end of main, drop C, drop A.

For a lifetime challenge drill, I sketch functions that return dangling references to locally-owned strings, triggering the missing-lifetime-specifier error, then note the fix is to return an owned String instead. I'm also planning a short coding exercise—something like implementing a `first_word` function that borrows a string and returns a slice—as a practical follow-up task.

Now I'm mapping out the day's full structure: total time budget around 180 minutes, with metadata at the top covering prerequisites, objectives, and pacing, followed by tiered hints before full solutions. I'm also double-checking the common Rust borrow-checker error codes like E0382 for moved values to make sure the exercises reference accurate diagnostics.

I'll keep exactly two content sections as required, with progressive hints and step-by-step reasoning plus generalization notes, then wrap up with a brief self-evaluation rubric and a short preview of Day 2, keeping the front matter and closing minimal so the structure stays within the requested two-section format.
</details>


**Claude**
# DAILY STUDY WORKSHEET — DAY 01

**Course Track:** M.Tech / B.Tech Advanced Core — Mathematics + Systems Programming
**Instructor:** Prof. (CSE) — Advanced Engineering Mathematics & Systems Programming
**Total Time Budget:** 180 minutes (2 h 30 m work + 30 m review)
**Sections:** 2 (mandatory, in order)

| Block | Section | Duration | Mode |
|---|---|---|---|
| 09:00–10:40 | § 1 Advanced Engineering Mathematics | 100 min | Pen & paper, no calculator |
| 10:40–10:50 | Break | 10 min | — |
| 10:50–11:50 | § 2 Rust Memory-Ownership Execution Drill | 60 min | Prediction first, compiler second |
| 11:50–12:00 | Self-scoring + error log | 10 min | Rubric at end |

**Ground Rule for Day 1:** For every problem, write your answer *before* looking at hints. Hints are tiered (H1 → H2 → H3). Consuming a hint costs marks in the rubric — that is intentional. In the Rust section, **predict the compiler's behaviour on paper before you run `rustc`.** Running first destroys the pedagogical value.

---

# SECTION 1 — ADVANCED ENGINEERING MATHEMATICS
## Theme of Day 1: *Linear Systems, Rank, Eigenstructure, and the Matrix Exponential*

**Learning Objectives (by end of section you must be able to):**
1. Use elementary row operations to decide consistency of a parametrized linear system via the Rank Criterion (Rouché–Capelli theorem).
2. Compute the characteristic polynomial through invariants (trace, sum of principal minors, determinant) instead of brute-force expansion.
3. Apply the Cayley–Hamilton theorem to obtain inverses and powers.
4. Prove the spectral properties of real symmetric matrices.
5. Solve a linear ODE system $\dot{X}=AX$ using structural decomposition of $A$.

**Standing Notation:** $A \in \mathbb{R}^{n\times n}$, $I$ = identity, $J$ = the all-ones matrix, $\rho(\cdot)$ or $\text{rank}(\cdot)$ = rank, $[A|b]$ = augmented matrix.

---

### Problem 1.1 — Consistency of a Parametrized System (15 min, 10 marks)

Consider the system in $x,y,z$ with real parameters $\lambda,\mu$:

$$
\begin{aligned}
x + y + z &= 6\\
x + 2y + 3z &= 10\\
x + 2y + \lambda z &= \mu
\end{aligned}
$$

**(a)** Determine all $(\lambda,\mu)$ for which the system has (i) a unique solution, (ii) infinitely many solutions, (iii) no solution.
**(b)** For case (ii), express the complete solution set in parametric vector form and state its geometric dimension.
**(c)** For case (i), give the explicit solution in terms of $\lambda,\mu$.

---

### Problem 1.2 — Eigenstructure via Invariants + Cayley–Hamilton (25 min, 15 marks)

Let
$$
A=\begin{bmatrix}2&1&1\\1&2&1\\1&1&2\end{bmatrix}.
$$

**(a)** Write the characteristic polynomial using the invariants $I_1=\text{tr}(A)$, $I_2=\sum$ (principal $2\times2$ minors), $I_3=\det A$ — **without** expanding a $3\times3$ determinant symbolically.
**(b)** Find all eigenvalues with algebraic and geometric multiplicities. Is $A$ diagonalizable? Justify with a theorem, not by computing $P$.
**(c)** Using the Cayley–Hamilton theorem, express $A^{-1}$ as a polynomial in $A$ and evaluate it explicitly.
**(d)** Derive a **closed form for $A^n$**, $n\in\mathbb{N}$.

---

### Problem 1.3 — Theory Proof: Spectral Property of Real Symmetric Matrices (15 min, 10 marks)

Let $A\in\mathbb{R}^{n\times n}$ with $A^{T}=A$.
**(a)** Prove that every eigenvalue of $A$ is real.
**(b)** Prove that eigenvectors corresponding to *distinct* eigenvalues are orthogonal.
**(c)** Verify (b) explicitly for the matrix of Problem 1.2 using the eigenvector for $\lambda=4$ and any eigenvector for $\lambda=1$.

---

### Problem 1.4 — Quadratic Form and Constrained Extremum (15 min, 10 marks)

With $A$ as in Problem 1.2, define $Q(\mathbf{x})=\mathbf{x}^{T}A\mathbf{x}$.
**(a)** Classify $Q$ (positive definite / semi-definite / indefinite). State the criterion used.
**(b)** Find $\max$ and $\min$ of $Q(\mathbf{x})$ subject to $\|\mathbf{x}\|_2=1$, and the maximizing/minimizing directions.
**(c)** Write $Q$ in the "sum of squares" form obtained from the structure $A=I+J$.

---

### Problem 1.5 — Linear ODE System via Matrix Exponential (20 min, 15 marks)

Solve the initial value problem
$$
\dot{X}(t)=AX(t),\qquad X(0)=\begin{bmatrix}1\\0\\0\end{bmatrix},
$$
with the same $A=\begin{bmatrix}2&1&1\\1&2&1\\1&1&2\end{bmatrix}$.

**(a)** Compute $e^{At}$ in closed form. **(b)** Write $X(t)$ componentwise. **(c)** State the asymptotic behaviour of $X(t)/\|X(t)\|$ as $t\to\infty$ and interpret it spectrally.

---

## § 1 — TIERED HINTS

> **Read one tier at a time. Stop as soon as you are unblocked.**

**P1.1**
- **H1:** Never take determinants first in a parametrized consistency problem. Reduce $[A|b]$ to row echelon form and *watch the last pivot*.
- **H2:** $R_2\to R_2-R_1$, $R_3\to R_3-R_1$, then $R_3\to R_3-R_2$. The final row becomes $[\,0\;\;0\;\;\lambda-3 \mid \mu-10\,]$.
- **H3:** Rouché–Capelli: unique $\iff \rho(A)=\rho([A|b])=n$; infinite $\iff \rho(A)=\rho([A|b])<n$ (with $n-\rho$ free parameters); inconsistent $\iff \rho(A)<\rho([A|b])$.

**P1.2**
- **H1:** Look at $A$ hard. Every diagonal entry is 2, every off-diagonal is 1. Can you write $A$ in terms of $I$ and the all-ones matrix $J$?
- **H2:** $A=I+J$ and $J^2=3J$ (since $J=\mathbf{1}\mathbf{1}^T$ and $\mathbf{1}^T\mathbf{1}=3$). $J$ has rank 1 ⇒ its eigenvalues are $3,0,0$.
- **H3:** If $\mu$ is an eigenvalue of $J$, then $1+\mu$ is an eigenvalue of $I+J$ with the *same* eigenvector. For (d), write $A^n=(I+J)^n$ and use $J^k=3^{k-1}J$ with the binomial theorem.

**P1.3**
- **H1:** For (a), allow complex $\lambda$, $\mathbf{v}\neq 0$: consider $\bar{\mathbf{v}}^{T}A\mathbf{v}$ and compute it in two ways.
- **H2:** For (b), start from $A\mathbf{u}=\lambda_1\mathbf{u}$, $A\mathbf{v}=\lambda_2\mathbf{v}$ and evaluate the scalar $\mathbf{v}^{T}A\mathbf{u}$ two ways using $A^{T}=A$.
- **H3:** You will reach $(\lambda_1-\lambda_2)\,\mathbf{v}^{T}\mathbf{u}=0$. Since $\lambda_1\neq\lambda_2$, conclude.

**P1.4**
- **H1:** Definiteness of a *symmetric* matrix is decided entirely by the sign of its eigenvalues — which you already computed in P1.2.
- **H2:** Rayleigh quotient: $\lambda_{\min}\le \dfrac{\mathbf{x}^TA\mathbf{x}}{\mathbf{x}^T\mathbf{x}}\le \lambda_{\max}$, extremes attained at the corresponding unit eigenvectors.
- **H3:** $\mathbf{x}^T(I+J)\mathbf{x}=\|\mathbf{x}\|^2+(\mathbf{1}^T\mathbf{x})^2$ since $J=\mathbf{1}\mathbf{1}^T$.

**P1.5**
- **H1:** $e^{A t}$ is easy when $A$ splits into commuting parts. Does $I$ commute with $J$?
- **H2:** $e^{(I+J)t}=e^{It}e^{Jt}=e^{t}e^{Jt}$ because $I$ commutes with everything.
- **H3:** Expand $e^{Jt}=I+\sum_{k\ge1}\frac{t^k}{k!}J^k$, substitute $J^k=3^{k-1}J$, and recognise the resulting scalar series as $\frac{e^{3t}-1}{3}$.

---

## § 1 — FULL SOLUTIONS WITH ANALYTICAL BREAKDOWN

### Solution 1.1

**Step 1 — Augment and reduce.**
$$
[A|b]=\begin{bmatrix}1&1&1&\big|&6\\1&2&3&\big|&10\\1&2&\lambda&\big|&\mu\end{bmatrix}
\xrightarrow[R_3-R_1]{R_2-R_1}
\begin{bmatrix}1&1&1&\big|&6\\0&1&2&\big|&4\\0&1&\lambda-1&\big|&\mu-6\end{bmatrix}
\xrightarrow{R_3-R_2}
\begin{bmatrix}1&1&1&\big|&6\\0&1&2&\big|&4\\0&0&\lambda-3&\big|&\mu-10\end{bmatrix}
$$

**Step 2 — Read off the trichotomy from the last row.** The last row encodes $(\lambda-3)z=\mu-10$.

| Case | Condition | $\rho(A)$ | $\rho([A|b])$ | Conclusion |
|---|---|---|---|---|
| (i) | $\lambda\neq 3$ | 3 | 3 | Unique solution ($=n$) |
| (ii) | $\lambda=3,\ \mu=10$ | 2 | 2 | Infinitely many, $3-2=1$ free parameter |
| (iii) | $\lambda=3,\ \mu\neq10$ | 2 | 3 | Inconsistent — row reads $0=\mu-10\neq0$ |

**(c) Unique solution ($\lambda\ne3$):** back-substitute.
$$z=\frac{\mu-10}{\lambda-3},\qquad y=4-2z,\qquad x=6-y-z=2+z.$$
So $\displaystyle \mathbf{x}=\Big(2+\tfrac{\mu-10}{\lambda-3},\ 4-\tfrac{2(\mu-10)}{\lambda-3},\ \tfrac{\mu-10}{\lambda-3}\Big)^T.$

**(b) Infinite family ($\lambda=3,\mu=10$):** set $z=t$. Then $y=4-2t$, $x=2+t$:
$$
\mathbf{x}(t)=\begin{bmatrix}2\\4\\0\end{bmatrix}+t\begin{bmatrix}1\\-2\\1\end{bmatrix},\quad t\in\mathbb{R}.
$$
Geometrically: a **line** (dimension 1) in $\mathbb{R}^3$ — the intersection of two distinct non-parallel planes (the third plane coincides with a combination of them). The direction vector $(1,-2,1)^T$ spans $\mathcal{N}(A)$, consistent with rank–nullity: $\dim\mathcal{N}(A)=3-2=1$.

**Analytical takeaway:** The general solution = *one particular solution* + *null space*. Rank analysis is the mechanised form of "how many independent constraints do I actually have?" Determinant methods (Cramer) collapse exactly at $\lambda=3$ and give you no information there — which is why the echelon route is professionally preferred.

---

### Solution 1.2

**Step 0 — Structural observation (the key move).**
$$A=I+J,\qquad J=\mathbf{1}\mathbf{1}^{T},\ \mathbf{1}=(1,1,1)^T,\qquad J^{2}=\mathbf{1}(\mathbf{1}^{T}\mathbf{1})\mathbf{1}^{T}=3J.$$

**(a) Characteristic polynomial by invariants.**
- $I_1=\text{tr}(A)=6$.
- $I_2=\sum$ principal $2\times2$ minors $=3\times\begin{vmatrix}2&1\\1&2\end{vmatrix}=3\times3=9$.
- $I_3=\det A$. Since eigenvalues (below) or by direct rank-one update: $\det(I+\mathbf{1}\mathbf{1}^T)=1+\mathbf{1}^T\mathbf{1}=4$ (matrix determinant lemma).

$$\boxed{\chi_A(\lambda)=\lambda^{3}-6\lambda^{2}+9\lambda-4=(\lambda-4)(\lambda-1)^2}$$

**(b) Spectrum.** $J$ has rank 1, so its eigenvalues are $3,0,0$; hence $A=I+J$ has eigenvalues $4,1,1$.
- $\lambda=4$: algebraic multiplicity 1 ⇒ geometric multiplicity 1. Eigenvector $\mathbf{1}=(1,1,1)^T$.
- $\lambda=1$: $A-I=J$, and $\text{rank}(J)=1$ ⇒ $\dim\mathcal{N}(A-I)=3-1=2$ = algebraic multiplicity.

Both multiplicities match ⇒ **$A$ is diagonalizable.** (Independent, stronger justification: $A$ is real symmetric, hence orthogonally diagonalizable by the Spectral Theorem — Problem 1.3.) Eigenspace for $\lambda=1$: $\{\mathbf{x}:\mathbf{1}^T\mathbf{x}=0\}$, e.g. $(1,-1,0)^T,(1,0,-1)^T$.

**(c) Inverse via Cayley–Hamilton.** CH gives $A^{3}-6A^{2}+9A-4I=0$. Multiply by $A^{-1}$ (legal since $\det A=4\ne0$):
$$A^{2}-6A+9I-4A^{-1}=0\ \Longrightarrow\ A^{-1}=\tfrac14\left(A^{2}-6A+9I\right).$$
Now $A^{2}=(I+J)^2=I+2J+J^2=I+5J$. Therefore
$$A^{-1}=\tfrac14\big(I+5J-6I-6J+9I\big)=\tfrac14\big(4I-J\big)=I-\tfrac14 J
=\frac14\begin{bmatrix}3&-1&-1\\-1&3&-1\\-1&-1&3\end{bmatrix}.$$
**Verification (mandatory habit):** $(I+J)(I-\tfrac14J)=I+J-\tfrac14J-\tfrac14(3J)=I+J-J=I$. ✔

**(d) Closed form for $A^n$.** Binomially, $A^{n}=(I+J)^{n}=I+\sum_{k=1}^{n}\binom{n}{k}J^{k}$ and $J^{k}=3^{k-1}J$:
$$A^{n}=I+\frac{J}{3}\sum_{k=1}^{n}\binom{n}{k}3^{k}=I+\frac{4^{n}-1}{3}J.$$
$$\boxed{A^{n}=I+\frac{4^{n}-1}{3}J}$$
**Sanity checks:** $n=1\Rightarrow I+J=A$ ✔; $n=2\Rightarrow I+5J$ ✔; $n=-1\Rightarrow I+\frac{1/4-1}{3}J=I-\frac14J$ ✔ (the formula analytically continues to negative integers).

**Analytical takeaway:** Recognising the algebraic *structure* ($I + $ rank-one) converted a $3\times3$ eigenvalue problem into two lines of arithmetic. In practice (PageRank, covariance shrinkage, graph Laplacians of complete graphs, MIMO channel models) the matrix $aI+bJ$ appears constantly. Memorise: eigenvalues $a+bn$ (once) and $a$ ($n-1$ times).

---

### Solution 1.3

**(a) Reality of eigenvalues.** Let $A\mathbf{v}=\lambda\mathbf{v}$, $\mathbf{v}\in\mathbb{C}^n\setminus\{0\}$, $\lambda \in \mathbb{C}$. Consider the scalar $s=\bar{\mathbf{v}}^{T}A\mathbf{v}$.
- Route 1: $s=\bar{\mathbf{v}}^{T}(\lambda \mathbf{v})=\lambda\,\|\mathbf{v}\|^{2}$.
- Route 2: $\bar{s}=\overline{\bar{\mathbf{v}}^{T}A\mathbf{v}}=\mathbf{v}^{T}\bar{A}\bar{\mathbf{v}}=\mathbf{v}^{T}A\bar{\mathbf{v}}$ (as $A$ real) $=(A\mathbf{v})^{T}\bar{\mathbf{v}}$ (as $A^{T}=A$) $=\lambda\mathbf{v}^{T}\bar{\mathbf{v}}=\lambda\|\mathbf{v}\|^{2}$… but also $\bar s = \bar\lambda\|\mathbf v\|^2$ from Route 1.

Hence $\lambda\|\mathbf{v}\|^{2}=\bar{\lambda}\|\mathbf{v}\|^{2}$ with $\|\mathbf{v}\|^{2}>0$ ⇒ $\lambda=\bar\lambda$ ⇒ $\lambda\in\mathbb{R}$. ∎

**(b) Orthogonality.** Let $A\mathbf{u}=\lambda_1\mathbf{u}$, $A\mathbf{v}=\lambda_2\mathbf{v}$, $\lambda_1\neq\lambda_2$ (both real by (a)). Evaluate the scalar $\mathbf{v}^{T}A\mathbf{u}$ two ways:
$$\mathbf{v}^{T}A\mathbf{u}=\mathbf{v}^{T}(\lambda_1\mathbf{u})=\lambda_1\mathbf{v}^{T}\mathbf{u};\qquad
\mathbf{v}^{T}A\mathbf{u}=(A^{T}\mathbf{v})^{T}\mathbf{u}=(A\mathbf{v})^{T}\mathbf{u}=\lambda_2\mathbf{v}^{T}\mathbf{u}.$$
Subtracting: $(\lambda_1-\lambda_2)\mathbf{v}^{T}\mathbf{u}=0$; since $\lambda_1\neq\lambda_2$, $\mathbf{v}^{T}\mathbf{u}=0$. ∎

**(c) Verification.** $\lambda=4$: $\mathbf{u}=(1,1,1)^T$. $\lambda=1$: $\mathbf{v}=(1,-1,0)^T$. Then $\mathbf{u}^T\mathbf{v}=1-1+0=0$ ✔. Note the $\lambda=1$ eigenspace is precisely $\mathbf{1}^{\perp}$ — the theorem is not a coincidence here, it is the geometry.

**Analytical takeaway:** The proof pattern "*compute one scalar in two ways using $A^T=A$*" is the single most reusable trick in matrix theory. It also yields: symmetric ⇒ orthogonally diagonalizable ⇒ quadratic forms reduce to principal axes (used immediately in P1.4).

---

### Solution 1.4

**(a) Classification.** $A$ symmetric with spectrum $\{4,1,1\}$, all $>0$ ⇒ **positive definite**. Cross-check with Sylvester's criterion (leading principal minors): $2>0$, $\begin{vmatrix}2&1\\1&2\end{vmatrix}=3>0$, $\det A=4>0$ ✔.

**(b) Constrained extrema (Rayleigh).** On $\|\mathbf{x}\|=1$:
$$\min Q=\lambda_{\min}=1 \text{ at any unit } \mathbf{x}\perp\mathbf{1}\ \big(\text{e.g. } \tfrac{1}{\sqrt2}(1,-1,0)^T\big),$$
$$\max Q=\lambda_{\max}=4 \text{ at } \mathbf{x}=\pm\tfrac{1}{\sqrt3}(1,1,1)^T.$$
*(Lagrange derivation: $\nabla(\mathbf{x}^TA\mathbf{x}-\lambda(\mathbf{x}^T\mathbf{x}-1))=0 \Rightarrow A\mathbf{x}=\lambda\mathbf{x}$, and then $Q=\lambda$. The stationary points of a quadratic form on the sphere are exactly the eigenvectors — the eigenproblem *is* the Lagrange condition.)*

**(c) Sum of squares.**
$$Q(\mathbf{x})=\mathbf{x}^{T}(I+\mathbf{1}\mathbf{1}^{T})\mathbf{x}=\|\mathbf{x}\|^{2}+(\mathbf{1}^{T}\mathbf{x})^{2}=x_1^2+x_2^2+x_3^2+(x_1+x_2+x_3)^2.$$
Manifestly $\ge0$, and $=0$ only at $\mathbf{x}=0$ ⇒ positive definite, re-proved in one line. Signature $(3,0)$; the rank-one term supplies the extra "+3" along $\mathbf 1$.

---

### Solution 1.5

**(a) Matrix exponential.** Since $I$ commutes with $J$, $e^{At}=e^{(I+J)t}=e^{t}\,e^{Jt}$. With $J^{k}=3^{k-1}J$:
$$e^{Jt}=I+\sum_{k\ge1}\frac{t^{k}}{k!}3^{k-1}J = I+\frac{J}{3}\left(\sum_{k\ge1}\frac{(3t)^{k}}{k!}\right)=I+\frac{e^{3t}-1}{3}J.$$
$$\boxed{\,e^{At}=e^{t}\left[I+\frac{e^{3t}-1}{3}J\right]\,}$$
*(Consistency with P1.2(d): both are of the form $I+cJ$ — the algebra $\{aI+bJ\}$ is closed under products and analytic functions.)*

**(b) Solution.** $X(t)=e^{At}X(0)$ with $X(0)=(1,0,0)^T$, and $J X(0)=(1,1,1)^T$:
$$X(t)=e^{t}\begin{bmatrix}1\\0\\0\end{bmatrix}+e^{t}\frac{e^{3t}-1}{3}\begin{bmatrix}1\\1\\1\end{bmatrix}
=\frac{1}{3}\begin{bmatrix}e^{4t}+2e^{t}\\ e^{4t}-e^{t}\\ e^{4t}-e^{t}\end{bmatrix}.$$
**Checks:** $X(0)=\frac13(3,0,0)^T=(1,0,0)^T$ ✔. $\dot{x}_1=\frac13(4e^{4t}+2e^t)$ and $(AX)_1=2x_1+x_2+x_3=\frac13(2e^{4t}+4e^t+2e^{4t}-2e^t)=\frac13(4e^{4t}+2e^t)$ ✔.

**(c) Asymptotics.** As $t\to\infty$, the $e^{4t}$ mode dominates:
$$\frac{X(t)}{\|X(t)\|}\longrightarrow \frac{1}{\sqrt3}(1,1,1)^{T}.$$
**Interpretation:** the trajectory aligns with the eigenvector of the **dominant eigenvalue** $\lambda_{\max}=4$ — this is precisely the power-iteration principle in continuous time. Growth rate $\sim e^{4t}$; the system is unstable (all $\text{Re}\,\lambda>0$). The transient $e^{t}$ modes (living in $\mathbf 1^{\perp}$) decay *relative to* the dominant mode at rate $e^{-3t}$; the spectral gap $\lambda_1-\lambda_2=3$ is exactly the convergence rate.

---

# SECTION 2 — RUST MEMORY-OWNERSHIP EXECUTION DRILL

**Objective:** Internalise the ownership/borrow model *as a compile-time decision procedure* you can execute mentally, before touching the compiler.

### The 6 Axioms (write these on your desk card)

| # | Rule |
|---|---|
| O1 | Every value has exactly **one owner**. |
| O2 | When the owner goes out of scope, the value is **dropped** (destructor runs). |
| O3 | Assignment/passing/returning of a non-`Copy` type is a **move**; the source becomes invalid. |
| O4 | Types implementing `Copy` (all primitive scalars, tuples/arrays of `Copy`) are **bit-copied**; source stays valid. |
| O5 | At any point, for a given value: **either** any number of `&T` **or** exactly one `&mut T` — never both. |
| O6 | A reference must never outlive its referent (no dangling). Borrows end at **last use** (NLL), not at end of scope. |

**Drill Protocol:** For each snippet write on paper: (1) **COMPILES / FAILS**; (2) if fails, the **error code** and one-line reason; (3) if compiles, the **exact stdout**. Only then run it (`rustc drill.rs` or the Playground).

---

### Drill 2.1 — The Canonical Move (3 min)

```rust
fn main() {
    let s1 = String::from("IIT");
    let s2 = s1;
    println!("{}, {}", s1, s2);
}
```
**Q:** Compiles? If not, which rule is violated and what is the minimal fix that keeps *both* names usable?

---

### Drill 2.2 — Copy vs. Clone (3 min)

```rust
fn main() {
    let a = 5;
    let b = a;
    let s = String::from("kgp");
    let t = s.clone();
    println!("{} {} {} {}", a, b, s, t);
}
```
**Q:** Predict stdout. Why is line 3 legal but Drill 2.1's line 3 not?

---

### Drill 2.3 — Moves Across Function Boundaries (5 min)

```rust
fn takes(s: String) -> usize { s.len() }
fn borrows(s: &String) -> usize { s.len() }

fn main() {
    let s = String::from("ownership");
    let n1 = borrows(&s);
    let n2 = takes(s);
    let n3 = borrows(&s);      // ← line X
    println!("{} {} {}", n1, n2, n3);
}
```
**Q:** (a) State the compile result. (b) Comment out line X — now what is the stdout? (c) Where exactly is the heap buffer freed in case (b)?

---

### Drill 2.4 — Aliasing + Reallocation (7 min)

```rust
fn main() {
    let mut v = vec![1, 2, 3];
    let first = &v[0];
    v.push(4);
    println!("first = {}", first);
}
```
**Q:** (a) Compile result + error code. (b) **What concrete memory bug is being prevented?** (c) Swap lines 4 and 5 (print before push): does it compile? Which rule explains the difference?

---

### Drill 2.5 — Shared then Exclusive Borrows (NLL) (5 min)

```rust
fn main() {
    let mut s = String::from("hello");
    let r1 = &s;
    let r2 = &s;
    println!("{} {}", r1, r2);
    let r3 = &mut s;
    r3.push_str(" world");
    println!("{}", r3);
}
```
**Q:** Compiles? Give stdout. Now move `let r3 = &mut s;` to immediately after `let r2 = &s;` — what happens and which error code?

---

### Drill 2.6 — Drop Order Trace (10 min) ★ *core of today's drill*

```rust
struct Noisy(&'static str);

impl Drop for Noisy {
    fn drop(&mut self) { println!("drop {}", self.0); }
}

fn consume(n: Noisy) { println!("consume {}", n.0); }

fn main() {
    let a = Noisy("A");
    let b = Noisy("B");
    consume(b);
    println!("end of main");
    let _c = Noisy("C");
}
```
**Q:** Write the **exact 5 lines** of stdout, in order. Justify each line with an axiom.

---

### Drill 2.7 — Dangling Reference / Lifetime (7 min) ★ challenge

```rust
fn make() -> &String {
    let s = String::from("transient");
    &s
}

fn main() { println!("{}", make()); }
```
**Q:** (a) Error code + reason. (b) Give **two** correct fixes with different ownership semantics. (c) Now write, from scratch, a compiling function
`fn first_word(s: &str) -> &str` returning the substring before the first space (whole string if none), and explain why **no explicit lifetime annotation** is required.

---

## § 2 — TIERED HINTS

**2.1** — H1: `String` owns a heap buffer; two owners would mean a double free. H2: The compiler marks `s1` as *moved-out*. H3: Fix = `let s2 = s1.clone();` (deep copy) or `let s2 = &s1;` (borrow, changes the type).

**2.2** — H1: `i32` implements `Copy`; `String` does not (it owns heap memory). H2: `Copy` ⇒ the move is a copy and the original remains valid. H3: `.clone()` performs an explicit deep copy — two independent heap buffers, two independent drops.

**2.3** — H1: `takes(s)` moves ownership into the callee. H2: When `takes` returns, its parameter `s` goes out of scope ⇒ drop runs *inside `takes`*. H3: After a move, any later `&s` is a *borrow of moved value* → E0382.

**2.4** — H1: `&v[0]` is a raw pointer into the Vec's heap buffer. H2: `push` may exceed capacity ⇒ `realloc` ⇒ old buffer freed. H3: The `&mut` needed by `push` conflicts with the live `&` (Axiom O5) → E0502. If the shared borrow's **last use** precedes the push, NLL ends it early and the code compiles.

**2.5** — H1: `r1`/`r2` are dead after the `println!` — NLL terminates them there. H2: If `&mut s` is created *while* `r1`,`r2` are still used later, the borrow regions overlap. H3: That overlap is E0502 ("cannot borrow `s` as mutable because it is also borrowed as immutable").

**2.6** — H1: Locals drop in **reverse declaration order** (LIFO) at scope end. H2: A moved-out variable is *not* dropped by the original scope; ownership left with the callee. H3: `_c` is declared last ⇒ dropped first; `b` was moved ⇒ already dropped inside `consume`.

**2.7** — H1: What does the returned reference point to after `make` returns? H2: The compiler cannot elide a lifetime for a return reference when there is **no input reference** to borrow from → E0106. H3: For (c): elision rule #2 — exactly one input lifetime ⇒ it is assigned to the output.

---

## § 2 — FULL SOLUTIONS WITH ANALYTICAL BREAKDOWN

### Solution 2.1 — FAILS: `E0382`

`String` is a fat pointer triple `{ptr, len, capacity}` on the stack pointing to a heap buffer. `let s2 = s1;` bit-copies the triple **and invalidates `s1`** (a *move*), so that when the scope ends only `s2`'s `drop` runs. If both stayed valid, both would free the same `ptr` ⇒ **double free**. The compiler reports:
`error[E0382]: borrow of moved value: 's1'`.

**Fixes and their cost model:**
| Fix | Semantics | Cost |
|---|---|---|
| `let s2 = s1.clone();` | 2 independent buffers, 2 drops | `O(n)` heap alloc + memcpy |
| `let s2 = &s1;` | `s2: &String`, `s1` still owner | Zero cost, but `s2` is now a reference |

**Mental model:** In C++ this compiles and gives you UB unless you remember `std::move`. Rust makes the move the *default* and the aliasing an *error*. This is the entire value proposition of ownership.

---

### Solution 2.2 — COMPILES. Output: `5 5 kgp kgp`

`i32: Copy` ⇒ `let b = a;` duplicates 4 bytes on the stack; nothing owns heap memory, so no double-free risk exists and `a` remains valid (Axiom O4). `String` deliberately does **not** implement `Copy` (a type cannot be `Copy` if it implements `Drop`), so duplication must be explicit and visible via `.clone()`. Rust's principle: **implicit operations must be cheap; expensive operations must be spelled out.**

---

### Solution 2.3

**(a) FAILS at line X:** `error[E0382]: borrow of moved value: 's'` — `takes(s)` moved ownership; `&s` afterwards is a borrow of a moved-out variable.

**(b) With line X removed:** compiles; stdout `9 9` — wait, careful: the `println!` also references `n3`. The intended edit is to remove **both** line X and `n3` from the format string; then stdout is:
```
9 9
```

**(c) Where is the buffer freed?** At the closing brace of `takes`, i.e. **inside the callee**, not in `main`. `takes` took ownership of the `String`; its parameter is a local that is dropped on return. `main`'s `s` is statically known to be moved-out, so `main` emits no drop for it (this is tracked by a compile-time *drop flag*; no runtime GC involved).

**Design principle:** Signature = contract.
- `fn f(s: String)` → "I consume it; you may not use it again."
- `fn f(s: &String)` / `&str` → "I only read it."
- `fn f(s: &mut String)` → "I mutate it exclusively for the duration."
Choosing the wrong one is the #1 API design error for beginners. Prefer `&str` over `&String` for read-only string parameters (accepts both `String` and literals via deref coercion).

---

### Solution 2.4

**(a) FAILS:** `error[E0502]: cannot borrow 'v' as mutable because it is also borrowed as immutable`.
Borrow timeline:
```
line 3: &v[0]      ── shared borrow begins ──┐
line 4: v.push(4)  ── requires &mut v ───────┼── CONFLICT (O5)
line 5: println!(first) ── last use ─────────┘
```

**(b) The bug prevented — use-after-free.** `Vec::push` with `len == capacity` allocates a larger buffer, `memcpy`s the elements, and **frees the old allocation**. `first` still points into the old (freed) block ⇒ dereferencing it in `println!` is a classic **dangling-pointer read**. In C++ this is the notorious *iterator invalidation* bug; it compiles silently and may work "most of the time" until capacity happens to be exhausted. Rust turns a probabilistic runtime UB into a deterministic compile error.

**(c) Print-then-push:**
```rust
let first = &v[0];
println!("first = {}", first);   // last use of the borrow
v.push(4);                        // OK
```
**Compiles.** Under **NLL (Non-Lexical Lifetimes)** a borrow's region ends at its *last use*, not at the end of the lexical block. After the `println!`, no live shared borrow remains, so the exclusive borrow for `push` is admissible. Output: `first = 1`.

---

### Solution 2.5 — COMPILES.

```
hello hello
hello world
```
`r1` and `r2` are two shared borrows — permitted, since shared borrows are read-only and hence non-conflicting. Their region ends at the `println!` (last use). The subsequent `&mut s` therefore begins in a region with no live shared borrows ⇒ legal.

**Perturbation:** placing `let r3 = &mut s;` before the `println!("{} {}", r1, r2)` makes the shared borrows live *across* the mutable borrow ⇒
`error[E0502]: cannot borrow 's' as mutable because it is also borrowed as immutable`.
Two simultaneous `&mut` would instead be `error[E0499]`.

**Why the rule exists (beyond safety):** `&mut T` is a *unique* pointer — the optimiser may assume no aliasing (equivalent to C's `restrict` applied everywhere, for free), enabling aggressive reordering/caching. Aliasing XOR mutability is simultaneously a memory-safety guarantee *and* a performance guarantee, plus it eliminates data races by construction (`Send`/`Sync` build on it).

---

### Solution 2.6 — Exact stdout

```
consume B
drop B
end of main
drop C
drop A
```

**Line-by-line justification:**

| Output | Reason |
|---|---|
| `consume B` | `consume(b)` **moves** `b` into the parameter `n`; body prints first. |
| `drop B` | `n` goes out of scope at the end of `consume` ⇒ `Drop::drop` runs **there** (O2, O3). |
| `end of main` | Plain statement executed next. |
| `drop C` | End of `main`: locals drop in **reverse declaration order**; `_c` was declared last ⇒ dropped first. |
| `drop A` | Then `a`. **`b` is skipped** — it was moved out, and the drop flag records that `main` no longer owns it. |

**Traps to note:** (1) Naming a binding `_c` still drops normally at scope end; naming it `_` (bare underscore) would drop it **immediately** — a genuine exam favourite. (2) `std::mem::drop(x)` is just `fn drop<T>(_: T) {}` — it works purely by taking ownership. (3) Temporaries drop at the end of the enclosing *statement*, which is why `let x = foo().lock().unwrap();` can deadlock in real code.

---

### Solution 2.7

**(a) FAILS:** `error[E0106]: missing lifetime specifier` — "this function's return type contains a borrowed value, but there is no value for it to be borrowed from." Even if you wrote `fn make() -> &'static String`, the deeper error appears: `error[E0515]: cannot return reference to local variable 's'`. `s`'s buffer is freed when `make` returns; the reference would dangle.

**(b) Two fixes with different semantics:**
```rust
// Fix 1 — transfer ownership to the caller (heap-allocated, caller drops it)
fn make() -> String { String::from("transient") }

// Fix 2 — return a reference to static read-only memory (no allocation, no drop)
fn make() -> &'static str { "transient" }
```
Fix 1 costs one allocation and gives a mutable, growable value. Fix 2 is zero-cost: the literal lives in the binary's `.rodata` for the whole program, so `'static` is genuinely satisfied.

**(c) Elision-driven function:**
```rust
fn first_word(s: &str) -> &str {
    match s.find(' ') {
        Some(i) => &s[..i],
        None => s,
    }
}
```
**Why no annotation is needed:** *Lifetime elision rule 2* — if there is exactly one input lifetime parameter, it is assigned to all output lifetime parameters. So the signature desugars to `fn first_word<'a>(s: &'a str) -> &'a str`, i.e. the returned slice is statically guaranteed not to outlive the input buffer. `&s[..i]` is a *slice*: a `{ptr, len}` fat pointer into the caller's memory — **no copy, no allocation**. This is why idiomatic Rust APIs take `&str` and return `&str`.

---

## CLOSING — SELF-ASSESSMENT & ERROR LOG

**Scoring (total 100):** § 1 = 60 marks (10+15+10+10+15) · § 2 = 40 marks (7 drills, ≈5.7 each: 2 for correct verdict, 2 for correct output/error code, 1.7 for the *reason*).
**Hint penalty:** −20 % of that problem's marks per hint tier consumed.

| Band | Score | Instruction |
|---|---|---|
| A | ≥ 85 | Proceed to Day 2. Attempt the stretch goals below. |
| B | 65–84 | Re-derive the two problems you lost most marks on, closed-book, tonight. |
| C | 45–64 | Re-do § 1 P1.2 and § 2 Drills 2.4/2.6 from scratch tomorrow before new material. |
| D | < 45 | Pause the schedule. Revise: Gaussian elimination + rank; Rust Book Ch. 4 in full. |

**Mandatory Error Log Format (one row per mistake):**
`Problem | What I wrote | Correct answer | Root cause (concept / algebra slip / misread) | Rule I will apply next time`

**Stretch goals (optional, 20 min):**
1. Generalise P1.2(d) to $A=aI+bJ_n$: show $A^k=a^kI+\frac{(a+bn)^k-a^k}{n}J_n$ and confirm the P1.5 exponential is its analytic continuation.
2. In Rust, implement `struct Stack<T>` with `push`, `pop -> Option<T>`, and `peek -> Option<&T>`. Explain why `peek` **must** return a reference while `pop` **must** return an owned value.

**Day 2 Preview:** § 1 — LU/QR factorisation, condition number $\kappa(A)$, least squares & normal equations. § 2 — Borrowing in `struct`s, explicit lifetime annotations, and `Box<T>` vs `Rc<T>` vs `RefCell<T>` (interior mutability and the runtime borrow check).


