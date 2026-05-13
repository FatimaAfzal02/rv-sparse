# RV-Sparse

A C implementation of sparse matrix-vector multiplication (`y = A * x`) using the Compressed Sparse Row (CSR) format.

This project converts a dense matrix into CSR representation and performs multiplication using only the non-zero elements of the matrix. The implementation avoids dynamic memory allocation completely and uses caller-provided buffers for all CSR data structures.

Sparse matrices usually contain a large number of zero values. Storing and processing every element wastes memory and computation time, especially when most entries are zero. CSR solves this by storing only the non-zero values along with their column indices and row offsets.

The CSR representation uses three arrays:

| Array | Description |
|---|---|
| `values[]` | stores non-zero matrix values |
| `col_indices[]` | stores the column index of each non-zero value |
| `row_ptrs[]` | stores where each row begins in the CSR arrays |

After conversion, matrix-vector multiplication is performed directly on the sparse representation.

## Features

*   Sparse matrix-vector multiplication
*   Dense to CSR conversion in a single pass
*   No `malloc()` or `calloc()`
*   Sequential memory access pattern
*   Caller-managed memory buffers
*   Handles invalid inputs safely
*   Randomized testing against dense reference computation

## Function Signature

```c
void sparse_multiply(
    int rows,
    int cols,
    const double* A,
    const double* x,
    int* out_nnz,
    double* values,
    int* col_indices,
    int* row_ptrs,
    double* y
);
```

## Parameters

| Parameter | Description |
|---|---|
| `rows`, `cols` | matrix dimensions |
| `A` | dense matrix stored in row-major order |
| `x` | input vector |
| `out_nnz` | total number of non-zero elements |
| `values` | output array containing CSR values |
| `col_indices` | output array containing CSR column indices |
| `row_ptrs` | output array containing CSR row offsets |
| `y` | output vector |

## Build

```bash
gcc -lm -o run challenge.c
```

## Run

```bash
./run
```

## Example Output

```text
Iter  0 [ 15x23, density=0.23, nnz=79 ]: PASS (Max error: 0.00e+00)
Iter  1 [ 28x19, density=0.18, nnz=95 ]: PASS (Max error: 0.00e+00)
Iter  2 [ 32x27, density=0.22, nnz=190]: PASS (Max error: 0.00e+00)
...
All tests passed! (100/100 iterations passed)
```

## Implementation Notes

The dense matrix is scanned row-by-row and converted into CSR format in a single pass.

A value is treated as non-zero using:

```c
val != 0.0
```

Only non-zero entries are stored and processed during multiplication.

The multiplication step iterates over the CSR arrays:

```text
y[i] += values[k] * x[col_indices[k]]
```

This avoids unnecessary operations on zero elements.

## Testing

The included test harness generates random sparse matrices with:

*   dimensions ranging from `5x5` to `45x45`
*   densities between `5%` and `40%`

For each iteration:

1.  A dense reference result is computed
2.  The CSR implementation result is computed
3.  Both outputs are compared for correctness

All 100 randomized test cases pass successfully.

## Future Improvements

Possible extensions include:

*   COO and CSC format support
*   SIMD optimization
*   Parallel sparse multiplication
*   RISC-V vector extension support
*   Matrix file loading
*   Performance benchmarking

## License

Open for educational and experimental use.
