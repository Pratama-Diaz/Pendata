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
