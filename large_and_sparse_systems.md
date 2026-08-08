# Large and Sparse Systems

## 1. Linear System

A linear system can be written as:

\[
Ax=b
\]

- **A** = coefficient matrix
- **x** = unknown vector
- **b** = known vector

There are two broad approaches:

### Direct methods
Examples:
- Gaussian elimination
- LU decomposition
- Cholesky decomposition

They obtain the solution through elimination or factorization.

### Iterative methods
Examples:
- Jacobi
- Gauss-Seidel
- Conjugate Gradient (CG)
- GMRES

They start with an initial guess and gradually improve it.

---

## 2. Sparse Matrix

A **sparse matrix** is a matrix in which most entries are zero.

Example:

\[
A=
\begin{bmatrix}
5&0&0&2&0\\
0&7&0&0&0\\
0&0&3&0&4\\
1&0&0&6&0\\
0&0&8&0&9
\end{bmatrix}
\]

A dense matrix, in contrast, has relatively few zero entries.

### Why sparsity matters

If \(n=10,000\), then:

\[
10,000\times10,000=100,000,000
\]

So the matrix contains **100 million entries**.

If most are zero, storing all of them is wasteful. Sparse storage therefore stores only the nonzero values and their positions.

---

## 3. Fill-in Terms

This is the main concept.

When a sparse matrix is processed using a direct method such as **Gaussian elimination**, some entries that were originally zero can become nonzero.

These newly created nonzero entries are called:

\[
\boxed{\text{Fill-in terms}}
\]

Conceptually:

\[
\text{Original zero} \rightarrow \text{nonzero after elimination}
\]

### Why does fill-in happen?

Gaussian elimination combines rows using operations such as:

\[
R_i\leftarrow R_i-cR_j
\]

When rows are combined, positions that contained zero can acquire nonzero values.

---

## 4. Fill-in in the Given Slide

The original matrix is sparse:

\[
\begin{bmatrix}
a_{11}&0&a_{13}&a_{14}&0\\
0&a_{22}&0&a_{24}&0\\
a_{31}&0&a_{33}&0&a_{35}\\
a_{41}&a_{42}&0&a_{44}&0\\
0&0&a_{53}&0&a_{55}
\end{bmatrix}
\]

After elimination, it can become:

\[
\begin{bmatrix}
a_{11}&0&a_{13}&a_{14}&0\\
0&a_{22}&0&a_{24}&0\\
0&0&a'_{33}&a'_{34}&a'_{35}\\
0&0&0&a'_{44}&a'_{45}\\
0&0&0&0&a'_{55}
\end{bmatrix}
\]

Entries such as \(a'_{34}\) and \(a'_{45}\) may be nonzero even though those positions were zero originally.

Therefore:

\[
\boxed{\text{New nonzero entries = Fill-in}}
\]

---

## 5. Why Fill-in is a Problem

Initially:

\[
A=\text{sparse matrix}
\]

So it can be stored and processed efficiently.

After elimination:

\[
A\rightarrow U
\]

where \(U\) is an upper-triangular matrix.

The matrix can become less sparse:

\[
\text{sparse}\rightarrow\text{less sparse}
\]

This causes:

- Increased memory usage
- More arithmetic operations
- Increased storage requirements
- Potentially higher computation time

Therefore:

\[
\boxed{\text{Fill-in}\Rightarrow\text{more memory}}
\]

and

\[
\boxed{\text{Fill-in}\Rightarrow\text{more computation}}
\]

---

## 6. Why Direct Methods Become Inefficient

The complete chain is:

1. The original matrix contains many zeros.
2. Sparse storage exploits those zeros.
3. Gaussian elimination is applied.
4. New nonzero entries appear.
5. These are fill-in terms.
6. The matrix becomes less sparse.
7. More entries must be stored.
8. More calculations are required.
9. The direct method can become expensive.

In short:

\[
\boxed{
\text{Large + Sparse}
\rightarrow
\text{Direct elimination}
\rightarrow
\text{Fill-in}
\rightarrow
\text{Higher cost}
}
\]

---

## 7. Sparse Matrix Storage

Sparse matrices can be stored efficiently by storing only their nonzero entries.

For example:

\[
A=
\begin{bmatrix}
5&0&0&2\\
0&3&0&0\\
0&0&7&0\\
1&0&0&4
\end{bmatrix}
\]

There are 16 positions but only 6 nonzero values:

\[
5,2,3,7,1,4
\]

Sparse formats store these values together with their positions.

Common formats include:

- COO — Coordinate format
- CSR — Compressed Sparse Row
- CSC — Compressed Sparse Column

The key idea is:

\[
\boxed{\text{Don't waste memory storing zeros.}}
\]

---

## 8. Why \(n=10,000\) Matters

For:

\[
n=10,000
\]

we have:

\[
A\in\mathbb{R}^{10000\times10000}
\]

and therefore:

\[
10^8=100,000,000
\]

entries.

If each entry uses 8 bytes, dense storage requires approximately:

\[
100,000,000\times8=800,000,000\text{ bytes}
\]

or about:

\[
\boxed{800\text{ MB}}
\]

for just one matrix.

This shows why exploiting sparsity is extremely important for large systems.

---

## 9. Iterative Methods

Instead of performing a complete elimination/factorization, an iterative method starts with an initial guess:

\[
x^{(0)}
\]

and gradually improves it:

\[
x^{(0)}
\rightarrow
x^{(1)}
\rightarrow
x^{(2)}
\rightarrow
\cdots
\rightarrow
x^{(k)}
\]

until:

\[
x^{(k)}\approx x
\]

The solution is approached through repeated calculations.

---

## 10. Why Iterative Methods Work Well with Sparse Matrices

A common operation is matrix-vector multiplication:

\[
Ap
\]

If \(A\) is sparse, this multiplication can use only the nonzero entries.

For example:

\[
\begin{bmatrix}
2&0&0\\
0&3&0\\
4&0&5
\end{bmatrix}
\begin{bmatrix}
x\\y\\z
\end{bmatrix}
=
\begin{bmatrix}
2x\\
3y\\
4x+5z
\end{bmatrix}
\]

The zero entries do not require useful arithmetic.

Thus, iterative methods can continue exploiting the original sparse structure.

---

## 11. Residual

For:

\[
Ax=b
\]

the residual is:

\[
r=b-Ax
\]

It measures how well the current approximation satisfies the system.

An iterative method updates the solution repeatedly. Conceptually:

\[
x_{\text{new}}=x_{\text{old}}+\alpha p
\]

where:

- \(x_{\text{old}}\) = current approximation
- \(p\) = update/search direction
- \(\alpha\) = step size

---

## 12. Connection to Conjugate Gradient

The **Conjugate Gradient (CG)** method is particularly useful for systems:

\[
Ax=b
\]

where \(A\) is typically:

- Large
- Sparse
- Symmetric
- Positive definite

CG iteratively improves the solution:

\[
x_0\rightarrow x_1\rightarrow x_2\rightarrow\cdots\rightarrow x_k
\]

A key operation is:

\[
Ap_k
\]

Because \(A\) is sparse, this can be computed efficiently.

Thus CG is well suited to the type of large sparse systems discussed in these slides.

---

## 13. Direct vs Iterative Methods

| Direct Methods | Iterative Methods |
|---|---|
| Gaussian elimination, LU | CG, Jacobi, Gauss-Seidel, GMRES |
| Obtain solution through elimination/factorization | Gradually approach the solution |
| Can create fill-in | Can exploit original sparsity |
| Fill-in can increase memory | Usually memory-efficient for sparse systems |
| Good for many small/moderate systems | Particularly useful for very large sparse systems |

---

## 14. Important Clarification

Direct methods are **not bad**.

They are useful for many problems, especially smaller or moderately sized systems.

The important situation here is:

\[
\boxed{\text{VERY LARGE + SPARSE}}
\]

For such systems, fill-in can become a serious memory and computational problem.

Therefore, iterative methods are often preferred.

---

## 15. Complete Concept Flow

```text
                 LARGE LINEAR SYSTEM
                         |
                         v
                   Is it sparse?
                         |
                         v
               Most entries are ZERO
                         |
                         v
             Store only NONZERO entries
                         |
                         v
                Memory-efficient
                         |
             +-----------+-----------+
             |                       |
             v                       v
       Direct method           Iterative method
             |                       |
             v                       v
    Gaussian elimination      Repeated updates
             |                       |
             v                       v
        Fill-in occurs       Exploit sparsity
             |                       |
             v                       v
     More nonzero entries       Efficient
             |
             v
    More memory + computation
             |
             v
       Can become expensive
```

---

## 16. Exam-Style Answer

### Why are iterative methods preferred for large sparse systems?

In a large sparse linear system, most matrix elements are zero and can be stored efficiently by storing only the nonzero elements. However, direct methods such as Gaussian elimination can produce new nonzero elements during elimination, called **fill-in terms**. These fill-in terms increase storage requirements and computational cost.

Therefore, for very large sparse systems, **iterative methods** such as Conjugate Gradient are often preferred because they can exploit the sparsity of the original matrix and avoid excessive fill-in.

### Remember

\[
\boxed{
\text{Sparse matrix}
\rightarrow
\text{Direct elimination}
\rightarrow
\text{Fill-in}
\rightarrow
\text{More storage/computation}
}
\]

versus:

\[
\boxed{
\text{Sparse matrix}
\rightarrow
\text{Iterative method}
\rightarrow
\text{Exploit sparsity}
\rightarrow
\text{Efficient}
}
\]
