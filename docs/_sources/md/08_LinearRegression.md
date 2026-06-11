# Analisis data dengan Linear Regression

## Deskripsi tugas
Pada tugas kali ini, diberikan dataset berupa 

![dataset](/img/dataset.jpeg)

Dengan dataset tersebut terdapat 1 buah variabel independent dan 1 buah variabel dependent. Tugas kali ini fokus untuk mencari nilai Koefisien (B1) dan Interception (B0). Tujuan nya adalah untuk mencari garis linier terbaik untuk keseluruhan data

## Analisis Manual Koefisien dan Interception

### Normal equation
Untuk mencari nilai \(b_0\) dan juga \(b_1\) digunakan rumus Normal equation yaitu:


$$
\hat{\beta} = (X^T X)^{-1} X^T y
$$
Rumus ini digunakan untuk menentukan nilai dari \(b_0\) dan juga \(b_1\) nantinya

### Variabel X
Data diatas yang berada pada Variabel X selanjutnya akan dibuat dengan matriks, namun pada variable X ini akan terdapat kolom bias/intercept dengan nilai 1. itu adalah Dummy column placeholder for error variable b0


$$
X=
\begin{bmatrix}
1 & 2 \\
1 & 4 \\
1 & 3 \\
1 & 3 \\
1 & 3 \\
1 & 4 \\
1 & 5
\end{bmatrix}
$$

### Variable Y
Lakukan hal yang sama juga pada variable Y namun tanpa data dummy
$$
y=
\begin{bmatrix}
2 \\
3 \\
5 \\
4 \\
3 \\
5 \\
6
\end{bmatrix}
$$

### Hitung tranpose matriks \(X^T\)
Transpose dilakukan dengan mengubah baris menjadi kolom.

$$
X^T=
\begin{bmatrix}
1 & 1 & 1 & 1 & 1 & 1 & 1 \\
2 & 4 & 3 & 3 & 3 & 4 & 5
\end{bmatrix}
$$

### Hitung \(X^TX\)

$$
X^TX=
\begin{bmatrix}
1 & 1 & 1 & 1 & 1 & 1 & 1 \\
2 & 4 & 3 & 3 & 3 & 4 & 5
\end{bmatrix}
\begin{bmatrix}
1 & 2 \\
1 & 4 \\
1 & 3 \\
1 & 3 \\
1 & 3 \\
1 & 4 \\
1 & 5
\end{bmatrix}
$$

Hasil \(X^TX\)

$$
X^TX=
\begin{bmatrix}
7 & 24 \\
24 & 88
\end{bmatrix}
$$


### Hitung inverse \((X^TX)^{-1}\)

Gunakan rumus inverse matriks 2x2:

A^-1 = 1 / (ad - bc)

\[
\begin{bmatrix}
d & -b \\
-c & a
\end{bmatrix}
\]


Hasil Determinan:

\[
(7)(88)-(24)(24)
\]

\[
=616-576
\]

\[
=40
\]

Subtitusi ke rumus invers:
$$
(X^TX)^{-1} =
\left(
\frac{1}{40}
\right)
\begin{bmatrix}
88 & -24 \\
-24 & 7
\end{bmatrix}
$$


\begin{bmatrix}
2.2 & -0.6 \\
-0.6 & 0.175
\end{bmatrix}

### Hitung \(X^Ty\)


$$
X^Ty=
\begin{bmatrix}
1 & 1 & 1 & 1 & 1 & 1 & 1 \\
2 & 4 & 3 & 3 & 3 & 4 & 5
\end{bmatrix}
\begin{bmatrix}
2 \\
3 \\
5 \\
4 \\
3 \\
5 \\
6
\end{bmatrix}
$$

### Hitung Koefisien regresi

Hitung Koefisien Regresi \(\hat{\beta}\)

$$
\hat{\beta} = (X^TX)^{-1}(X^Ty)
$$

Substitusi nilai:

$$
\hat{\beta} =
\begin{bmatrix}
2.2 & -0.6 \\
-0.6 & 0.175
\end{bmatrix}
\times
\begin{bmatrix}
28 \\
102
\end{bmatrix}
$$

Perhitungan Elemen Pertama

$$
(2.2 \times 28) + (-0.6 \times 102)
$$

$$
= 61.6 - 61.2
$$

$$
= 0.4
$$

 Perhitungan Elemen Kedua

$$
(-0.6 \times 28) + (0.175 \times 102)
$$

$$
= -16.8 + 17.85
$$

$$
= 1.05
$$

Hasil Akhir

$$
\hat{\beta} =
\begin{bmatrix}
0.4 \\
1.05
\end{bmatrix}
$$

### Hasil akhir gambar linier

![linier](/img/linier.png)

## Implementasi dengan Python

### Import Library

```python
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
from sklearn.linear_model import LinearRegression
from sklearn.metrics import mean_squared_error, r2_score
```

### Input Dataset

![Data input](../img/linear-regression/data.png)

```python
# Dataset titik A sampai G dari GeoGebra
labels = ['A', 'B', 'C', 'D', 'E', 'F', 'G']
X_raw  = np.array([2, 4, 3, 3, 3, 4, 5], dtype=float)   # variabel bebas (x)
Y      = np.array([2, 3, 5, 4, 3, 5, 6], dtype=float)   # variabel terikat (y)

df = pd.DataFrame({'Titik': labels, 'x': X_raw, 'y': Y})
df = df.set_index('Titik')
print(df.to_string())
```

### Metode 1 - sklearn LinearRegression

![sklearn LinearRegression](../img/sklearn.png)

sklearn memerlukan input $X$ berbentuk 2D array (matriks kolom).

```python
X_2d = X_raw.reshape(-1, 1)   # ubah jadi shape (7, 1)

model = LinearRegression()
model.fit(X_2d, Y)

b0_sk = model.intercept_
b1_sk = model.coef_[0]

Y_pred_sk = model.predict(X_2d)

print('=' * 50)
print('METODE 1 -- sklearn LinearRegression')
print('=' * 50)
print(f'b0 (intercept)  : {b0_sk:.6f}')
print(f'b1 (koefisien)  : {b1_sk:.6f}')
print(f'Persamaan       : y = {b1_sk:.4f}x + {b0_sk:.4f}')
print(f'R2 Score        : {r2_score(Y, Y_pred_sk):.4f}')
print(f'MSE             : {mean_squared_error(Y, Y_pred_sk):.4f}')
```

### Metode 2 - Analitik Manual: $\hat{\beta} = (X^\top X)^{-1} X^\top Y$

![Analitik normal equation](../img/normaAnalyst.png)

**Step 1: Susun Matriks X dengan Kolom Dummy**

![Susun matriks X](../img/mSusunMatriks.png)

Kolom pertama berisi **1 semua** sebagai placeholder untuk koefisien $b_0$, kolom kedua berisi nilai prediktor $x$.

```python
ones  = np.ones((len(X_raw), 1))          # kolom dummy
X_mat = np.column_stack([ones, X_raw])     # matriks X: shape (7, 2)

print('Matriks X (kolom 1 = dummy, kolom 2 = prediktor):')
print(X_mat)
print(f'\nShape X: {X_mat.shape}  ->  {X_mat.shape[0]} baris, {X_mat.shape[1]} kolom')

print('\nVektor Y:')
print(Y)
```

**Step 2: Hitung $X^\top X$**

![Transpose X](../img/mTranspose.png)

Transpose berarti balik baris jadi kolom. Lalu kalikan $X^\top$ dengan $X$ untuk menghasilkan matriks $2 \times 2$ (square matrix).

```python
Xt  = X_mat.T      # Transpose X: shape (2, 7)
XtX = Xt @ X_mat   # perkalian matriks: (2,7) x (7,2) = (2,2)

print('Xt (Transpose X):')
print(Xt)
print()
print('XtX:')
print(XtX)
print()
print(f'  XtX[0,0] = jumlah kolom dummy x dummy = {int(XtX[0,0])}')
print(f'  XtX[0,1] = jumlah x = {int(XtX[0,1])}')
print(f'  XtX[1,1] = jumlah x^2 = {int(XtX[1,1])}')
```

**Step 3: Invers $(X^\top X)^{-1}$**

![Invers matriks](../img/mInvers.png)

Hanya matriks persegi yang dapat di-invers. Untuk matriks $2 \times 2$: swap diagonal, bagi determinan.

```python
# Hitung determinan secara manual
a, b = XtX[0, 0], XtX[0, 1]
c, d = XtX[1, 0], XtX[1, 1]

det = a * d - b * c
print(f'Determinan = ({a:.0f} x {d:.0f}) - ({b:.0f} x {c:.0f})')
print(f'           = {a*d:.0f} - {b*c:.0f}')
print(f'           = {det:.0f}')
print()

# Invers manual 2x2: (1/det) * [[d, -b], [-c, a]]
XtX_inv_manual = (1 / det) * np.array([[d, -b], [-c, a]])
print('(XtX)^-1 -- perhitungan manual:')
print(XtX_inv_manual)
print()

# Verifikasi dengan numpy
XtX_inv = np.linalg.inv(XtX)
print('(XtX)^-1 -- numpy.linalg.inv (verifikasi):')
print(XtX_inv)
print()
match = np.allclose(XtX_inv_manual, XtX_inv)
print(f'Manual vs numpy: {"IDENTIK" if match else "BERBEDA"}')
```

**Step 4: Hitung $X^\top Y$**

![Hitung X transpose Y](../img/mXtY.png)

```python
XtY = Xt @ Y   # (2,7) x (7,) = (2,)

print('XtY:')
print(f'  XtY[0] = sum(1 * yi) = {" + ".join(str(int(v)) for v in Y)} = {int(XtY[0])}')
xiyistr = ' + '.join(f'{int(xi)}x{int(yi)}' for xi, yi in zip(X_raw, Y))
print(f'  XtY[1] = sum(xi * yi) = {xiyistr} = {int(XtY[1])}')
print()
print(f'XtY = {XtY}')
```

**Step 5: Hitung $\hat{\beta} = (X^\top X)^{-1} \cdot X^\top Y$**

```python
beta = XtX_inv @ XtY   # (2,2) x (2,) = (2,)

b0_an = beta[0]
b1_an = beta[1]

print('=' * 50)
print('METODE 2 -- Analitik Normal Equation')
print('=' * 50)
print(f'beta = {beta}')
print()
print(f'b0 (intercept)  : {b0_an:.6f}')
print(f'b1 (koefisien)  : {b1_an:.6f}')
print(f'Persamaan       : y = {b1_an:.4f}x + {b0_an:.4f}')

print()
print('Detail perhitungan:')
print(f'  b0 = ({XtX_inv[0,0]:.4f} x {XtY[0]:.0f}) + ({XtX_inv[0,1]:.4f} x {XtY[1]:.0f})')
print(f'     = {XtX_inv[0,0]*XtY[0]:.4f} + ({XtX_inv[0,1]*XtY[1]:.4f}) = {b0_an:.4f}')
print(f'  b1 = ({XtX_inv[1,0]:.4f} x {XtY[0]:.0f}) + ({XtX_inv[1,1]:.4f} x {XtY[1]:.0f})')
print(f'     = {XtX_inv[1,0]*XtY[0]:.4f} + {XtX_inv[1,1]*XtY[1]:.4f} = {b1_an:.4f}')
```

### Verifikasi - Kedua Metode Harus Sama

![Verifikasi hasil](../img/verif.png)

```python
print('=' * 50)
print('VERIFIKASI')
print('=' * 50)
print(f'{"Metode":<15} {"b0":>12} {"b1":>12}')
print('-' * 42)
print(f'{"sklearn":<15} {b0_sk:>12.6f} {b1_sk:>12.6f}')
print(f'{"Analitik":<15} {b0_an:>12.6f} {b1_an:>12.6f}')
print('-' * 42)
ok_b0 = 'SAMA' if abs(b0_sk - b0_an) < 1e-9 else 'BEDA'
ok_b1 = 'SAMA' if abs(b1_sk - b1_an) < 1e-9 else 'BEDA'
print(f'{"Status":<15} {ok_b0:>12} {ok_b1:>12}')
```

### Visualisasi

```python
x_line = np.linspace(1.5, 5.5, 200)
y_line = b1_an * x_line + b0_an

fig, axes = plt.subplots(1, 2, figsize=(13, 5))

# Plot 1: Scatter + garis regresi + residual
ax = axes[0]
ax.plot(x_line, y_line, color='crimson', linewidth=2,
        label=f'y_hat = {b1_an:.2f}x + {b0_an:.2f}', zorder=3)
ax.scatter(X_raw, Y, color='royalblue', s=90, zorder=5,
           label='Data A-G')
for xi, yi, ypi, lbl in zip(X_raw, Y, Y_pred, labels):
    # garis residual
    ax.plot([xi, xi], [yi, ypi], color='gray',
            linestyle='--', linewidth=1, alpha=0.6)
    # label titik
    ax.annotate(lbl, (xi, yi), textcoords='offset points',
                xytext=(6, 4), fontsize=10, color='navy', fontweight='bold')
ax.set_xlabel('x', fontsize=12)
ax.set_ylabel('y', fontsize=12)
ax.set_title('Regresi Linier -- Titik A sampai G\n(garis putus = residual)', fontsize=11)
ax.legend(fontsize=10)
ax.grid(True, linestyle='--', alpha=0.4)
ax.set_xlim(1, 6)
ax.set_ylim(1, 7)

# Plot 2: Tabel hasil
ax2 = axes[1]
ax2.axis('off')

col_labels = ['Metode', 'b0', 'b1', 'Persamaan']
rows_data  = [
    ['sklearn',  f'{b0_sk:.4f}', f'{b1_sk:.4f}', f'y = {b1_sk:.2f}x + {b0_sk:.2f}'],
    ['Analitik', f'{b0_an:.4f}', f'{b1_an:.4f}', f'y = {b1_an:.2f}x + {b0_an:.2f}'],
    ['Status',   'SAMA',         'SAMA',           'IDENTIK'],
]
table = ax2.table(cellText=rows_data, colLabels=col_labels,
                  loc='center', cellLoc='center')
table.auto_set_font_size(False)
table.set_fontsize(10.5)
table.scale(1.3, 2.2)
for j in range(len(col_labels)):
    table[0, j].set_facecolor('#4472C4')
    table[0, j].set_text_props(color='white', fontweight='bold')
for j in range(len(col_labels)):
    table[3, j].set_facecolor('#E2EFDA')

ax2.set_title(f'Ringkasan Hasil\nR2 = {r2_score(Y, Y_pred):.4f}  |  MSE = {mean_squared_error(Y, Y_pred):.4f}',
              fontsize=11, pad=20)

plt.tight_layout()
plt.savefig('plot_regresi_AG.png', dpi=150, bbox_inches='tight')
plt.show()
print('Plot tersimpan -> plot_regresi_AG.png')
```

## Visualisasi Akhir

![Visualisasi regresi linier](../img/visualisasi.png)