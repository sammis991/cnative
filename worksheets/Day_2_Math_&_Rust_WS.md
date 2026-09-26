# DAILY STUDY WORKSHEET — DAY 02

**Course Track:** M.Tech / B.Tech Advanced Core — Mathematics + Systems Programming
**Instructor:** Prof. (CSE) — Advanced Engineering Mathematics & Systems Programming
**Total Time Budget:** 180 minutes (2 h 30 m work + 30 m review)
**Sections:** 2 (mandatory, in order)

| Block | Section | Duration | Mode |
|---|---|---|---|
| 09:00–10:40 | § 1 Advanced Engineering Mathematics | 100 min | Pen & paper, no calculator |
| 10:40–10:50 | Break | 10 min | — |
| 10:50–11:50 | § 2 Rust Lifetimes & Smart-Pointer Execution Drill | 60 min | Prediction first, compiler second |
| 11:50–12:00 | Self-scoring + error log | 10 min | Rubric at end |

**Ground Rules (unchanged):** Answer before reading hints. Each hint tier costs 20% of that problem's marks. In § 2, **predict on paper, then compile.**

> **Day 1 Errata (read before starting):**
> 1. *Sol. 1.3(a)*: Route 2 should read cleanly as: $\bar s=\mathbf v^T A\bar{\mathbf v}=(A\mathbf v)^T\bar{\mathbf v}=\lambda\|\mathbf v\|^2$, while conjugating Route 1 gives $\bar s=\bar\lambda\|\mathbf v\|^2$. Equating these gives $\lambda=\bar\lambda$.
> 2. *Sol. 2.3(b)*: The intended edit removes **both** line X and `n3` from the `println!`. The output is `9 9`.
>
> Log these as "notation / presentation" items. The underlying mathematics and verdicts are unchanged.

---

# SECTION 1 — ADVANCED ENGINEERING MATHEMATICS
## Theme of Day 2: *Matrix Factorisations, Conditioning, and Least Squares*

**Learning Objectives:**
1. Compute a Doolittle LU factorisation, use it to solve $A\mathbf x=\mathbf b$, and explain why pivoting is required.
2. Construct a thin QR factorisation by (classical) Gram–Schmidt.
3. Solve an overdetermined system in the least-squares sense via (i) normal equations and (ii) QR, and interpret the answer as an orthogonal projection.
4. Compute a condition number and use it to bound error amplification.
5. Prove why QR is numerically preferred to normal equations: $\kappa_2(A^TA)=\kappa_2(A)^2$.

**Standing Notation:** $\|\cdot\|_2$ is the Euclidean norm. $\|\cdot\|_\infty$ is the max-row-sum norm for matrices and the max-abs norm for vectors. $\kappa(A)=\|A\|\,\|A^{-1}\|$. $\sigma_i$ are singular values. "Full column rank" means $\text{rank}(A)=n$ for $A\in\mathbb R^{m\times n}$ with $m\ge n$.

---

### Problem 2.1 — LU Factorisation and Triangular Solves (20 min, 15 marks)

$$
A=\begin{bmatrix}2&1&1\\4&3&3\\8&7&9\end{bmatrix},\qquad \mathbf b=\begin{bmatrix}3\\7\\19\end{bmatrix}.
$$

**(a)** Find the Doolittle factorisation $A=LU$ ($L$ unit lower-triangular, $U$ upper-triangular). Record every multiplier $\ell_{ij}$.
**(b)** Solve $A\mathbf x=\mathbf b$ using forward substitution $L\mathbf y=\mathbf b$ followed by back substitution $U\mathbf x=\mathbf y$.
**(c)** Compute $\det A$ from the factorisation.
**(d)** Show that $B=\begin{bmatrix}0&1\\1&1\end{bmatrix}$ has **no** LU factorisation without row exchanges, and give its $PB=LU$ form. Then explain what goes wrong numerically with $B_\varepsilon=\begin{bmatrix}\varepsilon&1\\1&1\end{bmatrix}$ for tiny $\varepsilon$ when no pivoting is used.
**(e)** State the leading-order flop counts for factorisation and for each subsequent solve. Why do we never compute $A^{-1}$ to solve systems?

---

### Problem 2.2 — QR by Gram–Schmidt (15 min, 10 marks)

$$
A=\begin{bmatrix}1&0\\1&1\\1&2\\1&3\end{bmatrix}\in\mathbb R^{4\times 2}.
$$

**(a)** Apply classical Gram–Schmidt to the columns of $A$ to obtain $Q\in\mathbb R^{4\times2}$ with orthonormal columns and upper-triangular $R\in\mathbb R^{2\times2}$ such that $A=QR$.
**(b)** Verify $Q^TQ=I_2$ and $QR=A$.
**(c)** In one sentence each, state why *modified* Gram–Schmidt and Householder reflections are preferred in floating-point arithmetic.

---

### Problem 2.3 — Least-Squares Line Fit (20 min, 15 marks)

Fit $y=c_0+c_1t$ to the data $(t,y)=(0,1),(1,2),(2,2),(3,4)$. The design matrix is the $A$ of Problem 2.2, and $\mathbf b=(1,2,2,4)^T$.

**(a)** Form and solve the normal equations $A^TA\,\hat{\mathbf c}=A^T\mathbf b$.
**(b)** Re-solve using your QR from Problem 2.2: $R\hat{\mathbf c}=Q^T\mathbf b$. Confirm that the answers agree.
**(c)** Compute the projection $\mathbf p=A\hat{\mathbf c}$, the residual $\mathbf r=\mathbf b-\mathbf p$, and $\|\mathbf r\|_2^2$. Verify $A^T\mathbf r=\mathbf 0$ and interpret this geometrically.
**(d)** Without forming the $4\times4$ matrix, find $\text{tr}(P)$ where $P=A(A^TA)^{-1}A^T$.
**(e)** Predict $y$ at $t=4$.

---

### Problem 2.4 — Condition Number and Error Amplification (15 min, 10 marks)

Let $A_\varepsilon=\begin{bmatrix}1&1\\1&1+\varepsilon\end{bmatrix}$ with $\varepsilon=0.01$.

**(a)** Compute $A_\varepsilon^{-1}$ and $\kappa_\infty(A_\varepsilon)$, first symbolically in $\varepsilon$ and then numerically.
**(b)** Take $\mathbf b=(2,\,2.01)^T$, which has exact solution $\mathbf x=(1,1)^T$. Perturb it to $\mathbf b'=(2,\,2.02)^T$. Solve for $\mathbf x'$. Compute the relative change in $\mathbf b$ and in $\mathbf x$ (both in $\infty$-norm), and the amplification factor. Check this against the bound $\dfrac{\|\delta\mathbf x\|}{\|\mathbf x\|}\le\kappa(A)\dfrac{\|\delta\mathbf b\|}{\|\mathbf b\|}$.
**(c)** Estimate $\kappa_2(A_\varepsilon)$ and state roughly how many significant decimal digits you lose solving this system.
**(d)** A classmate claims "$\det A$ small ⇒ ill-conditioned." Refute this with a one-line counterexample.

---

### Problem 2.5 — Theory: Why Least Squares Works, and Why QR Wins (15 min, 10 marks)

Let $A\in\mathbb R^{m\times n}$ with $m\ge n$.

**(a)** Prove that $\hat{\mathbf x}$ minimises $\|A\mathbf x-\mathbf b\|_2$ **if and only if** $A^T(A\hat{\mathbf x}-\mathbf b)=\mathbf 0$.
**(b)** Prove that if $A$ has full column rank, then $A^TA$ is symmetric positive definite, so the minimiser is unique.
**(c)** Prove that $\kappa_2(A^TA)=\kappa_2(A)^2$, where $\kappa_2(A)=\sigma_{\max}/\sigma_{\min}$.
**(d)** Show that $P=A(A^TA)^{-1}A^T$ satisfies $P^T=P$ and $P^2=P$, and that $P=QQ^T$ when $A=QR$.

---

## § 1 — TIERED HINTS

**P2.1**
- **H1:** LU is simply Gaussian elimination with bookkeeping. Each multiplier $\ell_{ij}=a_{ij}/(\text{pivot})$ you use to zero an entry goes into $L$ at position $(i,j)$.
- **H2:** The first-column multipliers are $4/2$ and $8/2$. After step 1, row 3 becomes $[0,3,5]$.
- **H3:** For (d), try to write $\begin{bmatrix}0&1\\1&1\end{bmatrix}=\begin{bmatrix}1&0\\ \ell&1\end{bmatrix}\begin{bmatrix}u_{11}&u_{12}\\0&u_{22}\end{bmatrix}$ and match the $(1,1)$ and $(2,1)$ entries. For $B_\varepsilon$, compute $u_{22}=1-1/\varepsilon$ and ask what floating-point arithmetic does to it.

**P2.2**
- **H1:** $\mathbf q_1=\mathbf a_1/\|\mathbf a_1\|$. Then subtract from $\mathbf a_2$ its component along $\mathbf q_1$.
- **H2:** $r_{11}=\|\mathbf a_1\|$, $r_{12}=\mathbf q_1^T\mathbf a_2$, $r_{22}=\|\mathbf a_2-r_{12}\mathbf q_1\|$.
- **H3:** You should find $r_{11}=2$, $r_{12}=3$, $r_{22}=\sqrt5$.

**P2.3**
- **H1:** $A^TA$ contains $\left[\begin{smallmatrix}m&\sum t\\ \sum t&\sum t^2\end{smallmatrix}\right]$. $A^T\mathbf b$ contains $(\sum y,\ \sum ty)$.
- **H2:** $A^TA=\left[\begin{smallmatrix}4&6\\6&14\end{smallmatrix}\right]$ and $A^T\mathbf b=(9,18)^T$.
- **H3:** For (d), use $\text{tr}(XY)=\text{tr}(YX)$.

**P2.4**
- **H1:** For a $2\times2$ matrix, $\begin{bmatrix}a&b\\c&d\end{bmatrix}^{-1}=\frac1{ad-bc}\begin{bmatrix}d&-b\\-c&a\end{bmatrix}$.
- **H2:** $\|A^{-1}\|_\infty=\max_i\sum_j|(A^{-1})_{ij}|$. Also, $\delta\mathbf x=A^{-1}\delta\mathbf b$ exactly.
- **H3:** $A_\varepsilon$ is SPD, so $\kappa_2=\lambda_{\max}/\lambda_{\min}$. The trace is $2+\varepsilon$ and the determinant is $\varepsilon$. For (d), think about scaling.

**P2.5**
- **H1:** For (a), write $A\mathbf x-\mathbf b=A(\mathbf x-\hat{\mathbf x})+(A\hat{\mathbf x}-\mathbf b)$ and expand the squared norm.
- **H2:** For (b), $\mathbf x^TA^TA\mathbf x=\|A\mathbf x\|^2$.
- **H3:** For (c), use the SVD $A=U\Sigma V^T$, which gives $A^TA=V\Sigma^T\Sigma V^T$.

---

## § 1 — FULL SOLUTIONS WITH ANALYTICAL BREAKDOWN

### Solution 2.1

**(a) Elimination with bookkeeping.**

*Step 1 (column 1, pivot $2$):* $\ell_{21}=4/2=2$ and $\ell_{31}=8/2=4$.
- $R_2\leftarrow R_2-2R_1$ gives $[0,1,1]$.
- $R_3\leftarrow R_3-4R_1$ gives $[0,3,5]$.

*Step 2 (column 2, pivot $1$):* $\ell_{32}=3/1=3$.
- $R_3\leftarrow R_3-3R_2$ gives $[0,0,2]$.

$$
\boxed{L=\begin{bmatrix}1&0&0\\2&1&0\\4&3&1\end{bmatrix},\qquad U=\begin{bmatrix}2&1&1\\0&1&1\\0&0&2\end{bmatrix}}
$$

**Verification:**
- Row 3 of $LU$ is $4[2,1,1]+3[0,1,1]+1[0,0,2]=[8,7,9]$ ✔
- Row 2 of $LU$ is $2[2,1,1]+[0,1,1]=[4,3,3]$ ✔

**(b) Two triangular solves.**

*Forward substitution, $L\mathbf y=\mathbf b$:*
- $y_1=3$
- $y_2=7-2(3)=1$
- $y_3=19-4(3)-3(1)=4$

*Back substitution, $U\mathbf x=\mathbf y$:*
- $2x_3=4$, so $x_3=2$
- $x_2+x_3=1$, so $x_2=-1$
- $2x_1+x_2+x_3=3$, so $x_1=1$

$$\boxed{\mathbf x=(1,-1,2)^T}$$

**Check:** $A\mathbf x=(2-1+2,\ 4-3+6,\ 8-7+18)^T=(3,7,19)^T$ ✔

**(c)** $\det A=\det L\cdot\det U=1\cdot(2\cdot1\cdot2)=4$. The determinant is simply the product of the pivots. This is how every serious library computes determinants: an $O(n^3)$ elimination, never an $O(n!)$ cofactor expansion.

**(d) Pivoting.**

Matching entries in $B=LU$:
- The $(1,1)$ entry forces $u_{11}=0$.
- The $(2,1)$ entry then requires $\ell\cdot u_{11}=1$, i.e. $\ell\cdot 0=1$. This is impossible.

So no LU factorisation exists. Swapping rows with $P=\left[\begin{smallmatrix}0&1\\1&0\end{smallmatrix}\right]$ gives
$$PB=\begin{bmatrix}1&1\\0&1\end{bmatrix}=I\cdot U.$$

*The numerical disaster with $B_\varepsilon$ and no pivoting:*
- The multiplier is $\ell=1/\varepsilon$, which is huge.
- The second pivot is $u_{22}=1-1/\varepsilon$. In floating point with $\varepsilon=10^{-20}$, this rounds to $-1/\varepsilon$. The "1" is annihilated.
- Reconstructing gives $\hat L\hat U=\left[\begin{smallmatrix}\varepsilon&1\\1&0\end{smallmatrix}\right]\neq B_\varepsilon$. The $(2,2)$ entry has been entirely lost, even though $B_\varepsilon$ is perfectly well-conditioned ($\kappa\approx 2.6$).

*The remedy is partial pivoting:* at each step, swap in the row with the largest $|a_{ik}|$. This guarantees $|\ell_{ij}|\le1$, which keeps element growth under control.

**(e) Cost model.**

| Operation | Leading flop count |
|---|---|
| LU factorisation | $\tfrac23n^3$ |
| Each solve (forward + back) | $2n^2$ |
| Forming $A^{-1}$ | $\approx 2n^3$ |

Forming $A^{-1}$ is roughly 3× the cost of LU. It is also *less accurate*, because it adds rounding error and then requires a matrix–vector product. With $k$ right-hand sides, LU costs $\tfrac23n^3+2kn^2$: you factor once and solve cheaply forever after. **Rule: "Never invert; factor."**

**Analytical takeaway:** $L$ is a *record of the elimination*. $U$ is its *result*. The whole of direct linear algebra (LU, Cholesky, QR) is "factor into structured pieces that are cheap to invert."

---

### Solution 2.2

**(a)** Let $\mathbf a_1=(1,1,1,1)^T$ and $\mathbf a_2=(0,1,2,3)^T$.

*First column:*
- $r_{11}=\|\mathbf a_1\|=2$
- $\mathbf q_1=\tfrac12(1,1,1,1)^T$

*Second column:*
- $r_{12}=\mathbf q_1^T\mathbf a_2=\tfrac12(0+1+2+3)=3$
- $\mathbf w=\mathbf a_2-3\mathbf q_1=(0,1,2,3)-(1.5,1.5,1.5,1.5)=(-1.5,-0.5,0.5,1.5)^T$
- $r_{22}=\|\mathbf w\|=\sqrt{2.25+0.25+0.25+2.25}=\sqrt5$
- $\mathbf q_2=\dfrac{1}{2\sqrt5}(-3,-1,1,3)^T$

$$
\boxed{Q=\begin{bmatrix}\tfrac12&-\tfrac{3}{2\sqrt5}\\[2pt]\tfrac12&-\tfrac{1}{2\sqrt5}\\[2pt]\tfrac12&\tfrac{1}{2\sqrt5}\\[2pt]\tfrac12&\tfrac{3}{2\sqrt5}\end{bmatrix},\qquad R=\begin{bmatrix}2&3\\0&\sqrt5\end{bmatrix}}
$$

**(b) Checks.**
- $\mathbf q_1^T\mathbf q_2=\tfrac{1}{4\sqrt5}(-3-1+1+3)=0$ ✔
- $\|\mathbf q_2\|^2=\tfrac{9+1+1+9}{20}=1$ ✔
- Column 2 of $QR$ is $3\mathbf q_1+\sqrt5\,\mathbf q_2=(1.5,1.5,1.5,1.5)+(-1.5,-0.5,0.5,1.5)=(0,1,2,3)$ ✔

**(c)**
- **Modified Gram–Schmidt** subtracts projections one at a time from the *updated* vector. Rounding errors are therefore not reprojected, and loss of orthogonality scales like $\varepsilon_{\text{mach}}\kappa(A)$ rather than $\varepsilon_{\text{mach}}\kappa(A)^2$.
- **Householder** builds $Q$ as a product of exact orthogonal reflectors $H=I-2\mathbf v\mathbf v^T/\mathbf v^T\mathbf v$. This makes $Q$ orthogonal to machine precision *regardless* of conditioning. It is what LAPACK's `dgeqrf` does.

**Analytical takeaway:** Gram–Schmidt is centring in disguise. Subtracting the $\mathbf q_1$ component from $\mathbf t=(0,1,2,3)$ produced $\mathbf t-\bar t\,\mathbf 1$, where $\bar t=1.5$. Orthogonalising against the constant vector *is* mean-removal. This is why regression on centred data decouples the intercept.

---

### Solution 2.3

**(a) Normal equations.**
$$
\begin{bmatrix}4&6\\6&14\end{bmatrix}\begin{bmatrix}c_0\\c_1\end{bmatrix}=\begin{bmatrix}9\\18\end{bmatrix},\qquad\det=56-36=20.
$$
$$c_0=\frac{9\cdot14-6\cdot18}{20}=\frac{18}{20}=0.9,\qquad c_1=\frac{4\cdot18-6\cdot9}{20}=\frac{18}{20}=0.9.$$
$$\boxed{\hat y=0.9+0.9\,t}$$

**(b) Via QR.**
- $Q^T\mathbf b$ has first entry $\mathbf q_1^T\mathbf b=\tfrac12(1+2+2+4)=4.5$.
- Its second entry is $\mathbf q_2^T\mathbf b=\tfrac1{2\sqrt5}(-3-2+2+12)=\tfrac{9}{2\sqrt5}$.

Back-substitute in $R\hat{\mathbf c}=Q^T\mathbf b$:
- $\sqrt5\,c_1=\tfrac9{2\sqrt5}$, so $c_1=0.9$ ✔
- $2c_0+3(0.9)=4.5$, so $c_0=0.9$ ✔

The two methods agree.

**(c) Projection and residual.**
- $\mathbf p=(0.9,\,1.8,\,2.7,\,3.6)^T$
- $\mathbf r=\mathbf b-\mathbf p=(0.1,\,0.2,\,-0.7,\,0.4)^T$
- $\|\mathbf r\|^2=0.01+0.04+0.49+0.16=\boxed{0.70}$

Orthogonality checks:
- $\mathbf 1^T\mathbf r=0.1+0.2-0.7+0.4=0$ ✔
- $\mathbf t^T\mathbf r=0+0.2-1.4+1.2=0$ ✔

**Geometry:** $\mathbf b\in\mathbb R^4$ is split into $\mathbf p\in\mathcal C(A)$ (a 2-D plane) and $\mathbf r\in\mathcal C(A)^\perp=\mathcal N(A^T)$. The best approximation is the foot of the perpendicular. Two statistical corollaries follow:
- Residuals sum to zero whenever an intercept is included.
- Residuals are uncorrelated with the regressor.

**(d)** $\text{tr}(P)=\text{tr}\big(A(A^TA)^{-1}A^T\big)=\text{tr}\big((A^TA)^{-1}A^TA\big)=\text{tr}(I_2)=2$. In statistics this is the "degrees of freedom of the fit" (the number of parameters), and $m-\text{tr}(P)=2$ is the residual degrees of freedom.

**(e)** At $t=4$: $\hat y=0.9+3.6=4.5$.

---

### Solution 2.4

**(a)** $\det A_\varepsilon=\varepsilon$, so
$$A_\varepsilon^{-1}=\frac1\varepsilon\begin{bmatrix}1+\varepsilon&-1\\-1&1\end{bmatrix}\overset{\varepsilon=0.01}{=}\begin{bmatrix}101&-100\\-100&100\end{bmatrix}.$$

The norms are:
- $\|A_\varepsilon\|_\infty=2+\varepsilon$
- $\|A_\varepsilon^{-1}\|_\infty=\dfrac{2+\varepsilon}{\varepsilon}$

$$\boxed{\kappa_\infty=\frac{(2+\varepsilon)^2}{\varepsilon}=\frac{2.01^2}{0.01}=404.01}$$

**(b) Perturbation experiment.**
- $\delta\mathbf b=(0,0.01)^T$, so $\delta\mathbf x=A^{-1}\delta\mathbf b=(-1,1)^T$ and $\mathbf x'=(0,2)^T$.
- Relative change in $\mathbf b$: $\dfrac{0.01}{2.01}\approx 0.004975$ (about 0.5%).
- Relative change in $\mathbf x$: $\dfrac{\|(-1,1)\|_\infty}{\|(1,1)\|_\infty}=1$ (100%).
- **Amplification factor:** $\dfrac{1}{0.004975}=201$.

Checking the bound: $\kappa_\infty\cdot0.004975=404.01\times0.004975\approx2.01\ge1$ ✔. The bound holds, and is within a factor of 2 of being tight.

**(c)** The eigenvalues are $\lambda_\pm=\tfrac12\big[(2+\varepsilon)\pm\sqrt{4+\varepsilon^2}\big]$. This gives $\lambda_{\max}\approx2.00501$ and $\lambda_{\min}\approx0.0049875$.

So $\kappa_2\approx\boxed{402}$ (asymptotically $\approx 4/\varepsilon$).

**Rule of thumb:** you lose about $\log_{10}\kappa\approx2.6$ significant digits. In double precision (≈16 digits) you retain ≈13, which is fine. In single precision (≈7) you retain only ≈4.

**(d) Counterexample:** $A=10^{-3}I_{10}$ has $\det A=10^{-30}$, yet $\kappa(A)=1$ (perfectly conditioned). The determinant scales with $c^n$ under $A\mapsto cA$. The condition number is **scale-invariant**.

**Analytical takeaway:** $\kappa$ measures how close $A$ is to singular, *relative to its own size*:
$$\frac{1}{\kappa_2(A)}=\min\left\{\frac{\|\delta A\|_2}{\|A\|_2}:\ A+\delta A\text{ singular}\right\}.$$
$A_\varepsilon$ is a relative distance ≈ 1/400 from the singular matrix $\left[\begin{smallmatrix}1&1\\1&1\end{smallmatrix}\right]$. Geometrically, its two rows are nearly parallel lines whose intersection slides wildly when a line is nudged.

---

### Solution 2.5

**(a)** Let $\mathbf e=A\hat{\mathbf x}-\mathbf b$. For any $\mathbf x$, write $\mathbf d=\mathbf x-\hat{\mathbf x}$. Then
$$\|A\mathbf x-\mathbf b\|^2=\|A\mathbf d+\mathbf e\|^2=\|A\mathbf d\|^2+2\,\mathbf d^T A^T\mathbf e+\|\mathbf e\|^2.$$

- (⇐) If $A^T\mathbf e=\mathbf 0$, then $\|A\mathbf x-\mathbf b\|^2=\|A\mathbf d\|^2+\|\mathbf e\|^2\ge\|\mathbf e\|^2$. So $\hat{\mathbf x}$ is a minimiser. This is Pythagoras.
- (⇒) If $\hat{\mathbf x}$ is a minimiser, set $\mathbf d=-t\,A^T\mathbf e$ with $t>0$. The expression becomes $\|\mathbf e\|^2-2t\|A^T\mathbf e\|^2+O(t^2)$. If $A^T\mathbf e\ne\mathbf 0$, this is strictly less than $\|\mathbf e\|^2$ for small $t$, contradicting minimality. ∎

**(b)** Symmetry: $(A^TA)^T=A^TA$. For $\mathbf x\ne\mathbf 0$, $\mathbf x^TA^TA\mathbf x=\|A\mathbf x\|^2$, and this is $>0$ because full column rank means $\mathcal N(A)=\{\mathbf 0\}$. So $A^TA$ is SPD, hence invertible, and the normal equations have exactly one solution. ∎

**(c)** Write $A=U\Sigma V^T$ with singular values $\sigma_1\ge\dots\ge\sigma_n>0$. Then
$$A^TA=V(\Sigma^T\Sigma)V^T,$$
which is an eigendecomposition with eigenvalues $\sigma_i^2$. Since $A^TA$ is SPD,
$$\kappa_2(A^TA)=\frac{\sigma_1^2}{\sigma_n^2}=\kappa_2(A)^2.$$ ∎

**Consequence:** If $\kappa_2(A)=10^6$, the normal equations face $\kappa=10^{12}$ and lose ≈12 of 16 digits. QR works with $R$, where $\kappa_2(R)=\kappa_2(A)$ because $Q$ preserves norms, so it loses only ≈6. **Normal equations square your problem's difficulty.**

**(d)**
- Symmetry: $P^T=A\big((A^TA)^{-1}\big)^TA^T=P$, because $A^TA$ is symmetric.
- Idempotence: $P^2=A(A^TA)^{-1}(A^TA)(A^TA)^{-1}A^T=P$.
- With $A=QR$: $A^TA=R^TR$, so
$$P=QR(R^TR)^{-1}R^TQ^T=QRR^{-1}R^{-T}R^TQ^T=QQ^T.$$ ∎

**Analytical takeaway:** Least squares is orthogonal projection. The normal equations are the *orthogonality condition* written algebraically. QR is the *numerically honest* way to impose it.

---

# SECTION 2 — RUST LIFETIMES & SMART-POINTER EXECUTION DRILL

**Objective:** Extend the Day 1 ownership model to three situations:
- **references stored inside data structures** (lifetimes),
- **heap indirection** (`Box`),
- **shared ownership and interior mutability** (`Rc`, `RefCell`, `Weak`).

### The 6 Axioms of Day 2 (new desk card)

| # | Rule |
|---|---|
| L1 | Lifetime annotations **describe** relationships between borrows. They **never** extend how long a value lives. |
| L2 | A struct holding `&'a T` cannot outlive the value it borrows: `Struct<'a>` is valid only within `'a`. |
| L3 | **Elision:** (1) each input reference gets its own lifetime; (2) exactly one input lifetime ⇒ it flows to all outputs; (3) if there is a `&self`/`&mut self`, its lifetime flows to all outputs. |
| L4 | `Box<T>`: **single owner**, heap-allocated, pointer-sized. Needed for recursive types and trait objects. |
| L5 | `Rc<T>`: **multiple owners** with a runtime reference count. Access is **shared (immutable) only**. The value is dropped when the strong count reaches 0. Single-threaded (`Arc<T>` for threads). |
| L6 | `RefCell<T>`: borrow rules are checked at **runtime**. Violation ⇒ **panic**, not a compile error. `Rc<RefCell<T>>` = shared + mutable. Reference cycles leak; break them with `Weak<T>`. |

**Drill Protocol (unchanged):** For each drill, write on paper (1) COMPILES / FAILS / **PANICS**, (2) the error code and reason, (3) the exact stdout. Only then run it.

---

### Drill 2.1 — A Struct That Borrows (4 min)

```rust
struct Excerpt {
    part: &str,
}

fn main() {
    let novel = String::from("Call me Ishmael. Some years ago...");
    let first = novel.split('.').next().unwrap();
    let e = Excerpt { part: first };
    println!("{}", e.part);
}
```
**Q:**
- (a) Verdict and error code.
- (b) Give the minimal fix and the resulting stdout.
- (c) Add the method `fn announce(&self, msg: &str) -> &str { println!("{}", msg); self.part }` to your fixed struct. Does it need explicit lifetimes? Which elision rule applies?

---

### Drill 2.2 — The Struct Outlives Its Source (5 min)

```rust
struct Excerpt<'a> { part: &'a str }

fn main() {
    let e;
    {
        let text = String::from("Call me Ishmael. Some years ago...");
        e = Excerpt { part: text.split('.').next().unwrap() };
    }
    println!("{}", e.part);
}
```
**Q:**
- (a) Verdict and error code.
- (b) Give three *structurally different* fixes.

---

### Drill 2.3 — `longest` and the Intersection of Lifetimes (8 min)

```rust
fn longest<'a>(x: &'a str, y: &'a str) -> &'a str {
    if x.len() >= y.len() { x } else { y }
}

fn main() {
    let s1 = String::from("long string");
    let result;
    {
        let s2 = String::from("xyz");
        result = longest(s1.as_str(), s2.as_str());
    }
    println!("{}", result);
}
```
**Q:**
- (a) Verdict and error code. At runtime `result` would point to `s1`, which is still alive, so why does the compiler reject this?
- (b) Move the `println!` inside the inner block. What is the verdict and output?
- (c) Delete all `'a` annotations. What error do you get, and why can't elision help here?
- (d) Change the signature to `fn longest<'a>(x: &'a str, y: &str) -> &'a str`, keeping the body. Where does the error appear now: at the call site or in the function?

---

### Drill 2.4 — Recursive Types Need `Box` (6 min)

```rust
enum List { Cons(i32, List), Nil }
fn main() {}
```
**Q:**
- (a) Verdict and error code, with a size argument.
- (b) After fixing, predict the output of:

```rust
use List::{Cons, Nil};
enum List { Cons(i32, Box<List>), Nil }

fn sum(l: &List) -> i32 {
    match l { Cons(v, rest) => v + sum(rest), Nil => 0 }
}

fn main() {
    let l = Cons(1, Box::new(Cons(2, Box::new(Cons(3, Box::new(Nil))))));
    println!("{}", sum(&l));
}
```
- (c) Why does `sum(rest)` type-check when `rest: &Box<List>` but `sum` expects `&List`?

---

### Drill 2.5 — `Rc` Reference-Count Trace (7 min)

```rust
use std::rc::Rc;

fn main() {
    let a = Rc::new(String::from("shared"));
    println!("after a     = {}", Rc::strong_count(&a));
    let b = Rc::clone(&a);
    println!("after b     = {}", Rc::strong_count(&a));
    {
        let c = Rc::clone(&a);
        println!("in scope    = {}", Rc::strong_count(&c));
    }
    println!("after scope = {}", Rc::strong_count(&a));
    drop(b);
    println!("after drop  = {}", Rc::strong_count(&a));
}
```
**Q:**
- (a) Give the exact 5 lines of stdout.
- (b) When is the `String`'s heap buffer freed?
- (c) Insert `a.push_str("!");` anywhere. What is the verdict and error code?
- (d) Why is `Rc::clone(&a)` preferred stylistically over `a.clone()`?

---

### Drill 2.6 — `RefCell`: Borrow Checking at Runtime (8 min)

```rust
use std::cell::RefCell;

fn main() {
    let cell = RefCell::new(vec![1, 2, 3]);
    {
        let r = cell.borrow();
        println!("len = {}", r.len());
    }
    cell.borrow_mut().push(4);
    println!("{:?}", cell.borrow());

    let r1 = cell.borrow();
    let mut w = cell.borrow_mut();
    w.push(5);
    println!("{:?}", r1);
}
```
**Q:**
- (a) Does it **compile**?
- (b) Give the exact stdout and the runtime behaviour, including which line fails and why.
- (c) Rewrite the last four lines so they succeed.
- (d) Give a non-panicking API for probing borrow state.

---

### Drill 2.7 — Reference Cycles and `Weak` (10 min) ★ challenge

```rust
use std::cell::RefCell;
use std::rc::Rc;

struct Node {
    name: &'static str,
    next: RefCell<Option<Rc<Node>>>,
}

impl Drop for Node {
    fn drop(&mut self) { println!("drop {}", self.name); }
}

fn main() {
    let a = Rc::new(Node { name: "A", next: RefCell::new(None) });
    let b = Rc::new(Node { name: "B", next: RefCell::new(Some(Rc::clone(&a))) });
    *a.next.borrow_mut() = Some(Rc::clone(&b));
    println!("a strong = {}, b strong = {}", Rc::strong_count(&a), Rc::strong_count(&b));
    println!("end of main");
}
```
**Q:**
- (a) Give the exact stdout. Pay special attention to what happens *after* `end of main`.
- (b) Explain the outcome with reference counts. Is this undefined behaviour?
- (c) Re-type `next` using `Weak<Node>` so that both nodes are dropped. Give the new exact stdout.
- (d) In a tree with parent pointers, which direction should be `Rc` and which `Weak`, and why?

---

## § 2 — TIERED HINTS

**2.1**
- **H1:** A reference inside a struct must declare *whose* lifetime it borrows.
- **H2:** Use `struct Excerpt<'a> { part: &'a str }`.
- **H3:** For (c), rule 3: `&self` is present, so its lifetime is assigned to the return value.

**2.2**
- **H1:** `text` dies at the inner `}`. When does `e` last use it?
- **H2:** This is Axiom L2. The error is "borrowed value does not live long enough."
- **H3:** The fixes are: shorten the use, lengthen the owner, or stop borrowing entirely (own the data).

**2.3**
- **H1:** The compiler checks the *call* using only the *signature*, never the body.
- **H2:** With one `'a` on both inputs, `'a` is instantiated as the *shorter* of the two borrows, so the result is valid only that long.
- **H3:** For (c), there are two input lifetimes and no `&self`, so rule 2 does not apply and E0106 is raised. For (d), the check moves *into the function body*.

**2.4**
- **H1:** To compute `size_of::<List>()`, you need `size_of::<List>()`.
- **H2:** `Box<List>` is a pointer, so it has a fixed size (8 bytes on 64-bit).
- **H3:** Consider deref coercion: `&Box<T>` → `&T`.

**2.5**
- **H1:** `Rc::clone` increments the count. Dropping an `Rc` decrements it.
- **H2:** `c` is dropped at its block's `}`, and `drop(b)` is explicit.
- **H3:** `Rc<T>` only implements `Deref`, not `DerefMut`.

**2.6**
- **H1:** `borrow()` returns `Ref<T>` and `borrow_mut()` returns `RefMut<T>`. These are guards that hold the borrow until they are dropped.
- **H2:** Temporaries inside a statement are dropped at the end of that statement.
- **H3:** `r1` is still alive when `borrow_mut()` is called, and `r1` is used afterwards.

**2.7**
- **H1:** Count owners. How many `Rc` handles point to A after the assignment on line 3 of `main`?
- **H2:** At the end of `main`, the local handles are dropped. Each count goes from 2 to 1, not to 0.
- **H3:** `Weak` does not contribute to the strong count. Use `Rc::downgrade(&x)` to create one and `.upgrade()` to access it, which returns `Option<Rc<T>>`.

---

## § 2 — FULL SOLUTIONS WITH ANALYTICAL BREAKDOWN

### Solution 2.1 — FAILS: `E0106` (missing lifetime specifier)

**(a)** A struct field `&str` without a lifetime is meaningless to the compiler. Elision applies only to *function signatures*, never to type definitions. The compiler must know which scope the borrow is tied to in order to enforce L2 at every use site.

**(b) Fix:**
```rust
struct Excerpt<'a> { part: &'a str }
```
Output:
```
Call me Ishmael
```
The expression `novel.split('.').next().unwrap()` returns a `&str` slice into `novel`'s heap buffer. It is zero-copy.

**(c)** No explicit annotations are needed:
```rust
impl<'a> Excerpt<'a> {
    fn announce(&self, msg: &str) -> &str { println!("{}", msg); self.part }
}
```
There are two input lifetimes (`&self` and `msg`), so rule 2 cannot decide. **Rule 3** applies: the lifetime of `&self` is assigned to the output.

**Subtle point:** Returning `msg` instead would **fail**, because the elided output lifetime is tied to `self`, not to `msg`. Elision is a *default*, not inference. If the default is wrong, you must annotate.

---

### Solution 2.2 — FAILS: `E0597` (`text` does not live long enough)

**(a)** Borrow timeline:
```
{ let text = ...           ── owner born
  e = Excerpt{ &text.. }   ── borrow 'a begins
}                          ── text DROPPED; buffer freed
println!(e.part)           ── use of 'a  → would read freed memory
```
The region `'a` must cover the `println!`, but `text` does not. This is a use-after-free prevented statically.

**(b) Three structural fixes:**

| Strategy | Change | Semantics |
|---|---|---|
| Shorten the borrow | Move `println!` inside the block | The borrower dies before the owner |
| Lengthen the owner | Declare `text` in the outer scope | The owner outlives the borrower |
| Stop borrowing | `struct Excerpt { part: String }` with `.to_string()` | The struct owns a copy, so there is no lifetime and one allocation |

**Design principle:** Borrowing structs (`Parser<'a>`, `Tokenizer<'a>`, iterators) are ideal as **short-lived views**. Long-lived or returned-from-function data should usually **own** its contents. When lifetime annotations start spreading through a codebase, that is often the signal to switch to owned data.

---

### Solution 2.3

**(a) FAILS: `E0597` — `s2` does not live long enough.**

The signature promises: "the output is valid for `'a`, where both inputs are valid for at least `'a`." At the call site, the compiler picks the largest `'a` satisfying both constraints, which is the lifetime of the `s2` borrow (the inner block). `result` is used after that, so the call is rejected.

The compiler does **not** execute the `if`. It cannot know that `x` wins, and in general the choice depends on runtime data. **Signatures are contracts. Call sites are checked against contracts, not implementations.** This is what makes Rust's borrow checking *modular*: changing a function's body can never break its callers.

**(b) Compiles.** Output:
```
long string
```
Both borrows are now alive at the use site.

**(c)** `error[E0106]: missing lifetime specifier`. There are two input references and no `&self`, so neither rule 2 nor rule 3 applies. The compiler refuses to guess whether the output borrows from `x`, from `y`, or from both.

**(d)** The error moves **into the function body**. Returning `y`, whose lifetime is unrelated to `'a`, where `&'a str` is promised gives:
```
error[E0621]: explicit lifetime required in the type of `y`
```
The call site in `main` would then be fine as far as `y` is concerned. **Lesson:** annotations shift the burden of proof. The author must implement the promise, and callers may rely on it.

---

### Solution 2.4

**(a) FAILS: `E0072` — recursive type `List` has infinite size.**

Compute the size: $\text{size}(\texttt{List})=\text{tag}+\text{size}(\texttt{i32})+\text{size}(\texttt{List})$. This has no finite solution. Rust values have statically known sizes (`Sized`) so they can live on the stack and be moved by `memcpy`.

**Fix:** use indirection. `Box<List>` is a fixed 8 bytes on 64-bit. (The compiler hint suggests exactly this: "insert some indirection, e.g. a `Box`.")

**(b) COMPILES.** Output:
```
6
```
Memory layout:
- The outer `Cons(1, _)` lives in `main`'s stack frame.
- Each nested node lives on the heap and is exclusively owned by the previous node's `Box`.
- At the end of `main`, dropping `l` recursively drops the chain: node 1's `Box` frees node 2, which frees node 3.

**(c)** Two ergonomic features apply:
- **Match ergonomics:** matching on `&List` binds `v: &i32` and `rest: &Box<List>`.
- **Deref coercion:** `Box<T>: Deref<Target=T>`, so `&Box<List>` coerces to `&List` automatically at the call site.

The expression `v + sum(rest)` uses `impl Add<i32> for &i32`.

**Bonus fact:** `size_of::<Option<Box<T>>>() == size_of::<Box<T>>()`. A `Box` is never null, so `None` is encoded as the null pointer (the *niche optimisation*). This gives zero-cost nullable pointers with no null-pointer bugs.

---

### Solution 2.5

**(a)** Exact stdout:
```
after a     = 1
after b     = 2
in scope    = 3
after scope = 2
after drop  = 1
```

| Line | Event | Count |
|---|---|---|
| 1 | `Rc::new` allocates `{strong, weak, String}` on the heap | 1 |
| 2 | `Rc::clone` copies the pointer and increments strong | 2 |
| 3 | Another clone, `c` | 3 |
| 4 | `c` is dropped at `}`, decrementing strong | 2 |
| 5 | `drop(b)` takes ownership of `b` and drops it | 1 |

**(b)** The `String`'s buffer is freed at the closing brace of `main`, when `a` is dropped and strong goes 1 → 0.
- Dropping the inner `String` frees its buffer.
- The `Rc` control block itself is freed once the weak count also reaches 0.

**(c) FAILS:** `error[E0596]: cannot borrow data in an 'Rc' as mutable`. `Rc<T>` implements `Deref` but not `DerefMut` (Axiom L5). Handing out `&mut String` through one handle while other handles exist would violate "aliasing XOR mutability."

There are two escape hatches:
- `Rc::get_mut(&mut a)` returns `Some(&mut T)` only if the strong count is 1.
- `Rc::make_mut(&mut a)` performs clone-on-write.

For true shared mutation, use `Rc<RefCell<T>>` (Drill 2.6).

**(d)** `a.clone()` *reads* like a deep copy. `Rc::clone(&a)` signals to reviewers that this is an O(1) count increment. The convention makes the cost model visible, following the Day 1 principle that expensive operations must be spelled out.

---

### Solution 2.6 — COMPILES, then PANICS at runtime

**(a)** Yes, it compiles. `borrow()` and `borrow_mut()` both take `&self`, so the static borrow checker sees only shared borrows of `cell`. The exclusivity check has been **deferred to runtime** (Axiom L6).

**(b)** stdout:
```
len = 3
[1, 2, 3, 4]
```
Then, on stderr, the program panics at the `cell.borrow_mut()` line with a message of the form `already borrowed: BorrowMutError`. The exact wording varies slightly across toolchain versions. The process exits with code 101. `w.push(5)` and the final `println!` **never execute**.

Line-by-line trace:

| Statement | RefCell borrow state | Result |
|---|---|---|
| `let r = cell.borrow()` inside the block | 1 reader | OK |
| `}` | `r` dropped, 0 readers | — |
| `cell.borrow_mut().push(4)` | Temporary `RefMut`, dropped at the `;` | OK |
| `println!(.., cell.borrow())` | Temporary `Ref`, dropped at the end of the statement | Prints `[1, 2, 3, 4]` |
| `let r1 = cell.borrow()` | 1 reader, **kept alive** | OK |
| `let mut w = cell.borrow_mut()` | Writer requested while a reader exists | **PANIC** |

**Important contrast with Day 1:** NLL does **not** help here. `r1` is a guard object whose `Drop` releases the runtime flag, and it is used later. Even if it were not used later, a `let`-bound guard lives until the end of scope.

**(c) Fix:** end the reader before taking the writer, either by scoping it or by calling `drop(r1)`:
```rust
{
    let r1 = cell.borrow();
    println!("{:?}", r1);
}                                   // r1 released
cell.borrow_mut().push(5);          // OK
```

**(d)** `cell.try_borrow_mut()` returns `Result<RefMut<T>, BorrowMutError>`. It returns `Err` instead of panicking. `try_borrow()` is the reader equivalent.

**Why `RefCell` exists:** Some valid programs cannot be proven safe statically. Examples include graphs, observer patterns, caches mutated behind `&self`, and mock objects. `RefCell` trades a compile-time guarantee for a runtime check (a small counter) plus the risk of a panic. The idiomatic shared-mutable pattern is:
```rust
let acct = Rc::new(RefCell::new(Account { balance: 100 }));
let alice = Rc::clone(&acct);
alice.borrow_mut().balance -= 30;   // many owners (Rc) × runtime-checked mutation (RefCell)
```
Keep `borrow_mut()` guards **as short as possible**. A good habit is to confine each one to a single statement.

---

### Solution 2.7

**(a)** Exact stdout:
```
a strong = 2, b strong = 2
end of main
```
**Nothing else is printed.** Neither `drop A` nor `drop B` appears.

**(b) The reference cycle.**
- A is owned by the local `a` and by `B.next`, so A's strong count is 2.
- B is owned by the local `b` and by `A.next`, so B's strong count is 2.

At the end of `main`:
- `b` is dropped (reverse declaration order), so B's count goes 2 → 1. B is still owned by A.
- `a` is dropped, so A's count goes 2 → 1. A is still owned by B.

Both counts are stuck at 1. The two heap nodes keep each other alive, with no external path to them. **This is a memory leak.**

It is **not undefined behaviour**. Rust's safety guarantee excludes *leaks* by design: `std::mem::forget` is a safe function. Rust guarantees no use-after-free, no double free, and no data races. It does not guarantee "every allocation is freed." Reference counting cannot reclaim cycles, which is the classic argument for tracing garbage collection. Rust's answer is to make you declare **ownership direction**.

**(c) Fix with `Weak`:**
```rust
use std::cell::RefCell;
use std::rc::{Rc, Weak};

struct Node {
    name: &'static str,
    next: RefCell<Weak<Node>>,
}

impl Drop for Node {
    fn drop(&mut self) { println!("drop {}", self.name); }
}

fn main() {
    let a = Rc::new(Node { name: "A", next: RefCell::new(Weak::new()) });
    let b = Rc::new(Node { name: "B", next: RefCell::new(Rc::downgrade(&a)) });
    *a.next.borrow_mut() = Rc::downgrade(&b);
    println!("a strong = {}, b strong = {}", Rc::strong_count(&a), Rc::strong_count(&b));
    println!("end of main");
}
```
New exact stdout:
```
a strong = 1, b strong = 1
end of main
drop B
drop A
```
The only strong owners are the locals.
- `b` is dropped first (reverse declaration order), so B's strong count reaches 0 and `drop B` prints.
- Then `a` is dropped, and `drop A` prints.

To follow a link, call `a.next.borrow().upgrade()`, which returns `Option<Rc<Node>>`. It returns `None` if the target has already been dropped. `Weak` is therefore a *non-owning, dangling-safe* pointer.

**(d)** In a tree:
- **Children are `Rc`**: `children: RefCell<Vec<Rc<Node>>>`. A parent *owns* its children, and dropping the root should free the whole tree.
- **Parents are `Weak`**: `parent: RefCell<Weak<Node>>`. A child *refers* to its parent but must not keep it alive.

**The rule:** ownership edges must form a DAG. Every back-edge in the graph is `Weak`. This is how DOM trees, scene graphs, and doubly linked lists (`next: Rc`, `prev: Weak`) are built in safe Rust.

### Smart-Pointer Decision Table (memorise)

| Need | Use | Owners | Mutation | Checked | Thread-safe analogue |
|---|---|---|---|---|---|
| Heap allocation, recursive type, `dyn Trait` | `Box<T>` | 1 | via owner (`&mut`) | compile time | `Box<T>` (`Send` if `T: Send`) |
| Shared read-only ownership | `Rc<T>` | many | ✗ | count at runtime | `Arc<T>` |
| Mutation through `&self` | `RefCell<T>` | 1 | ✓ interior | runtime (panic) | `Mutex<T>` / `RwLock<T>` |
| Shared + mutable | `Rc<RefCell<T>>` | many | ✓ | runtime | `Arc<Mutex<T>>` |
| Non-owning back-pointer | `Weak<T>` | 0 | via `upgrade()` | runtime (`Option`) | `sync::Weak<T>` |

---

## CLOSING — SELF-ASSESSMENT & ERROR LOG

**Scoring (total 100):**
- **§ 1 = 60 marks** (15 + 10 + 15 + 10 + 10).
- **§ 2 = 40 marks** (7 drills, ≈5.7 each). Each drill awards 2 for the correct verdict (compile / fail / **panic**), 2 for the exact output or error code, and 1.7 for the reason.
- **New trap category:** in Drill 2.6, answering "FAILS to compile" scores 0 for the verdict. Distinguishing compile-time from runtime failure is today's core skill.

**Hint penalty:** −20% of the problem's marks per tier consumed.

| Band | Score | Instruction |
|---|---|---|
| A | ≥ 85 | Proceed to Day 3. Attempt the stretch goals. |
| B | 65–84 | Tonight, re-derive P2.3 (both methods) and Drill 2.3 closed-book. |
| C | 45–64 | Tomorrow, before new material: redo P2.1, P2.5(a), and Drills 2.6 and 2.7 from scratch. |
| D | < 45 | Pause. Revise Gaussian elimination, orthogonal projection, and Rust Book Ch. 10.3 and Ch. 15 in full. |

**Error Log format (same as Day 1):**
`Problem | What I wrote | Correct answer | Root cause (concept / algebra slip / misread / compile-vs-runtime confusion) | Rule I will apply next time`

**Cumulative check:** Re-read yesterday's error log. If any Day 1 root cause recurred today, mark it **★ REPEAT**. Repeats get priority revision.

**Stretch goals (optional, 25 min):**
1. **Householder QR by hand:** For $\mathbf a_1=(1,1,1,1)^T$, construct $H=I-2\frac{\mathbf v\mathbf v^T}{\mathbf v^T\mathbf v}$ with $\mathbf v=\mathbf a_1+\|\mathbf a_1\|\mathbf e_1$ (sign chosen to avoid cancellation). Show that $H\mathbf a_1=-2\mathbf e_1$. Prove that $H$ is symmetric and orthogonal.
2. **Math × Rust bridge:** Implement `fn lu_solve(a: &mut [Vec<f64>], b: &mut [f64])`, an in-place Doolittle factorisation with partial pivoting followed by forward and back substitution. Test it on P2.1 and expect `[1.0, -1.0, 2.0]`. **Borrow-checker challenge:** you will need to read row $k$ while mutating row $i>k$. Explain why `a[i][j] -= a[i][k] * a[k][j]` compiles for `Vec<Vec<f64>>`, and why swapping rows is done with `a.swap(i, k)` rather than two `&mut` indexings. Look up `split_at_mut` and explain what problem it solves.
3. **Tree with parent links:** Build a 3-node tree using `Rc<RefCell<Vec<Rc<Node>>>>` for children and `RefCell<Weak<Node>>` for the parent. Print the strong and weak counts of the root before and after the children are attached.

**Day 3 Preview:**
- **§ 1:** Singular Value Decomposition, the Moore–Penrose pseudo-inverse, low-rank approximation (Eckart–Young theorem), and PCA.
- **§ 2:** Traits and generics, static versus dynamic dispatch (`impl Trait` vs `Box<dyn Trait>`), and closures with their capture modes (`Fn` / `FnMut` / `FnOnce`), which is ownership applied to captured environments.
