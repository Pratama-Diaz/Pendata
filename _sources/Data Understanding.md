# Data Understanding

Tahap Data Understanding merupakan fase awal dalam metodologi CRISP-DM setelah tahap Business Understanding selesai dilakukan. Pada tahap ini, fokus utama adalah memahami data yang akan digunakan dalam proses analisis sebelum dilakukan persiapan atau pemodelan lebih lanjut.

Tujuan utama dari tahap Data Understanding adalah memperoleh pemahaman menyeluruh terhadap data sehingga peneliti atau analis dapat menentukan langkah yang tepat pada tahap berikutnya, yaitu Data Preparation. Dengan pemahaman yang baik terhadap kondisi dan karakteristik data, proses analisis dapat dilakukan secara lebih terarah dan meminimalkan kesalahan dalam pembangunan model.

## 1. Sumber Data

Dataset yang digunakan dalam penelitian ini adalah Iris Flower Dataset yang diperoleh dari platform Kaggle. Dataset ini berisi data pengukuran morfologi bunga iris yang terdiri dari tiga spesies, yaitu Iris-setosa, Iris-versicolor, dan Iris-virginica. Dataset Iris Flower terdiri dari 150 data observasi dengan 5 atribut utama. Empat atribut pertama merupakan variabel numerik yang berisi hasil pengukuran fisik bunga, yaitu sepal length, sepal width, petal length, dan petal width. Keempat atribut tersebut bertipe numerik karena nilainya berupa angka hasil pengukuran dalam satuan tertentu. Atribut kelima adalah species, yang merupakan variabel kategorikal dan berfungsi sebagai label atau target dalam proses klasifikasi. 
[Dataset Iris](https://www.kaggle.com/datasets/arshid/iris-flower-dataset?resource=download)


## 2. Eksplorasi Dataset

Dalam proses mengidentifikasi dataset ini, salah satu tools yang dapat dimanfaatkan untuk pengidentifikasian nya adalah python

### 2.1 Connect Pyhton ke Database
#### 2.1.1 Connect MySQL ke Python
```
pip install mysql-connector-python
```
Sebelum melakukan koneksi antara MySQL ke Python, kita perlu setting environment yang diperlukan, salah satunya menginstall library diatas.

```
import mysql.connector
db = mysql.connector.connect(
    host="localhost",
    user="root",
    password="", 
    database="iris"
)
print("Berhasil terhubung ke MySQL!")

cursor = db.cursor()

cursor.execute("SELECT * FROM iris")

hasil = cursor.fetchall()

for data in hasil:
    print(data)

db.close()
```
Selanjutnya masukkan ini ke dalam file code python kita, pastikan bahwasan nya di database MySQL kita sudah ada database bernama iris, sesuai dengan nama file csv kita diatas

![original image](https://cdn.mathpix.com/snip/images/j21pH1tPPYzv5cb9M1SfZIXUWMuSU5-FnKstdUJJSTI.original.fullsize.png)

Hasil jika di running dan berhasil melakukan koneksi antara MySQL dan Python

#### 2.1.2 Connect PostgreSQL ke Python
```
pip install psycopg2
```
Sama seperti MySQL kita perlu men-setting environment untuk PostgreSQL kita juga dengan cara install library psycopg2 diatas

```
import psycopg2
conn = psycopg2.connect(
    host="localhost",
    database="postgres",
    user="postgres",
    password="",
    port="5432"
)
print("Berhasil connect ke PostgreSQL")
cursor = conn.cursor()

cursor.execute("SELECT * FROM iris")

rows = cursor.fetchall()
for row in rows:
    print(row)

cursor.close()
conn.close()
```
Masukkan contoh codingan diatas, jangan lupa untuk menyesuaikan nama database dan nama table yang dimiliki.

![original image](https://cdn.mathpix.com/snip/images/lTIjeqLGDRTGLD-mm_zBe_dtwWyys_RmKUCLRmMQYiI.original.fullsize.png)

Berikut adalah hasil jika kita sukses untuk melakukan koneksi database

#### 2.1.3 Connect PostgreSQL ke Orange
![original image](https://cdn.mathpix.com/snip/images/-KJeUqpmHj-zFuA481aumFKz6pdUVTHKyqNwDAbKeww.original.fullsize.png)
Berikut adalah hasil jika PostgreSQL dapat connect dengan aplikasi orange yang dimiliki

### 2.2 Persiapan library dan import file tanpa menggunakan database

```
from google.colab import drive
import pandas as pd
import matplotlib.pyplot as plt

drive.mount('/content/drive')
```
- import drive : Pengizinan pembacaan file dalam google drive, ini digunakan agar google collaboratory dapat membaca file IRIS.csv yang kita taruh di dalam google drive. 

- import pandas : Digunakan untuk menganalisis dan memanipulasi data yang dimiliki

- import matplotlib : Untuk melakukan visualisasi data

### 2.3 Menentukan path drive

```
path = "/content/drive/MyDrive/tugas/IRIS.csv"
df = pd.read_csv(path)
```

Dalam kasus ini file IRIS.csv berada dalam folder tugas, maka path kita arahkan menuju folder tugas, lalu gunakan library pandas untuk membaca isi file nya

### 2.4 Struktur Dataset

```
df.head()
```

Code diatas digunakan untuk menampilkan 5 data teratas yang ada di dalam file IRIS.csv
Berikut tampilan dari code di atas setelah di jalankan:


| index | sepal\_length | sepal\_width | petal\_length | petal\_width | species |
| :-- | :-- | :-- | :-- | :-- | :-- |
| 0 | 5\.1 | 3\.5 | 1\.4 | 0\.2 | Iris-setosa |
| 1 | 4\.9 | 3\.0 | 1\.4 | 0\.2 | Iris-setosa |
| 2 | 4\.7 | 3\.2 | 1\.3 | 0\.2 | Iris-setosa |
| 3 | 4\.6 | 3\.1 | 1\.5 | 0\.2 | Iris-setosa |
| 4 | 5\.0 | 3\.6 | 1\.4 | 0\.2 | Iris-setosa |

Setiap data memiliki 4 atribut numerik yang merepresentasikan ukuran bagian bunga dalam satuan centimeter (cm), yaitu:

1. Sepal Length – Panjang kelopak bunga
2. Sepal Width – Lebar kelopak bunga
3. Petal Length – Panjang mahkota bunga
4. Petal Width – Lebar mahkota bunga
Selain itu, terdapat satu atribut kategorikal yaitu:
5. Species – Label kelas yang menunjukkan jenis bunga iris yang terdiri dari tiga kategori:
    - Setosa
    - Versicolor
    - Virginica

### 2.5 Statistik Deskriptif Awal dan pengecekan jumlah class

```
df.describe()
kelas = df.iloc[:, -1].unique()
jumlah_kelas = df.iloc[:, -1].nunique()
print(kelas)
print("Jumlah kelas:", jumlah_kelas)
```

Berikut contoh statistik deskriptifnya dan jumlah kelas:


| index | sepal\_length | sepal\_width | petal\_length | petal\_width |
| :-- | :-- | :-- | :-- | :-- |
| count | 150\.0 | 150\.0 | 150\.0 | 150\.0 |
| mean | 5\.843333333333334 | 3\.0540000000000003 | 3\.758666666666666 | 1\.1986666666666668 |
| std | 0\.8280661279778629 | 0\.4335943113621737 | 1\.7644204199522617 | 0\.7631607417008414 |
| min | 4\.3 | 2\.0 | 1\.0 | 0\.1 |
| 25% | 5\.1 | 2\.8 | 1\.6 | 0\.3 |
| 50% | 5\.8 | 3\.0 | 4\.35 | 1\.3 |
| 75% | 6\.4 | 3\.3 | 5\.1 | 1\.8 |
| max | 7\.9 | 4\.4 | 6\.9 | 2\.5 |

- Iris-setosa
- Iris-versicolor
- Iris-virginica
**Jumlah kelas: 3**


 Berdasarkan hasil statistik deskriptif, dataset Iris terdiri dari 150 data pada setiap atribut. Nilai rata-rata menunjukkan bahwa ukuran sepal dan petal berada pada rentang yang wajar, dengan panjang sepal rata-rata sekitar 5.84 cm dan panjang petal sekitar 3.76 cm. Variasi data terbesar terdapat pada atribut petal length, sedangkan variasi terkecil terdapat pada sepal width, yang menunjukkan bahwa ukuran petal lebih beragam dibandingkan ukuran sepal. Rentang nilai minimum hingga maksimum pada setiap atribut masih berada dalam batas yang normal dan tidak menunjukkan adanya nilai ekstrem yang mencurigakan. 
 
 Standar deviasi tertinggi terdapat pada atribut petal length sebesar 1.76, yang menunjukkan bahwa variasi panjang petal lebih besar dibandingkan atribut lainnya. Nilai minimum dan maksimum menunjukkan rentang data yang masih dalam batas wajar, yaitu sepal length antara 4.3 hingga 7.9 cm dan petal width antara 0.1 hingga 2.5 cm. Selain itu, nilai kuartil (25%, 50%, dan 75%) menunjukkan distribusi data yang relatif stabil tanpa adanya penyimpangan ekstrem yang signifikan.

 Lalu nilai jumlah kelas pada dataset iris berjumlah 3 kelas dengan keterangan yang ada diatas
 ### 2.6 Pengecekan Data Duplikat

```
df.duplicated().sum()
```

Code di atas merupakan code untuk pengecekan data duplikat, berikut untuk hasil dari pengecekan data duplikat:

```
np.int64(3)
```

Jadi setelah dilakukan pengecekan, terdapat data duplikat yaitu ada 3 data duplikat.

### 2.7 Pengecekan Data Null

```
df.isnull().sum()
```

Berikut untuk tampilan hasil dari pengecekan data Null:


|  |  |
| :-- | :-- |
| sepal_length | 0 |
| sepal_width | 0 |
| petal_length | 0 |
| petal_width | 0 |
| species | 0 |

Hasil diatas menunjukan tidak adanya data Null, ditambah pada penfecekan deskriptif awal menunjukan bahwa seluruh data memiliki jumlah yang sama, artinya tidak ada data null di setiap atribut nya.

## 3. Analisa korelasi

Analisis korelasi digunakan untuk mengetahui hubungan antar variabel numerik dalam dataset. Korelasi menunjukkan seberapa kuat hubungan antara dua variabel serta arah hubungannya (positif atau negatif).

Nilai korelasi berada pada rentang:

- -1 → hubungan negatif sempurna

- 0 → tidak ada hubungan

- +1 → hubungan positif sempurna

Pada dataset Iris, analisis korelasi dilakukan pada empat atribut numerik, yaitu:

- sepal_length

- sepal_width

- petal_length

- petal_width
```
correlation_matrix = df.corr(numeric_only=True)
print(correlation_matrix)
```
Berdasarkan hasil perhitungan korelasi Pearson antar fitur numerik, diperoleh matriks korelasi sebagai berikut:

|               | sepal_length | sepal_width | petal_length | petal_width |
|---------------|--------------|-------------|--------------|-------------|
| sepal_length  | 1.000000     | -0.109369   | 0.871754     | 0.817954    |
| sepal_width   | -0.109369    | 1.000000    | -0.420516    | -0.356544   |
| petal_length  | 0.871754     | -0.420516   | 1.000000     | 0.962757    |
| petal_width   | 0.817954     | -0.356544   | 0.962757     | 1.000000    |

### Interpretasi

- petal_length dan petal_width (0.962757) memiliki korelasi positif yang sangat kuat. Hal ini menunjukkan bahwa semakin panjang petal, maka semakin lebar petal.
- sepal_length dan petal_length (0.871754) menunjukkan hubungan positif yang kuat.
- sepal_length dan petal_width (0.817954) juga memiliki korelasi positif yang kuat.
- sepal_width memiliki korelasi yang relatif lemah hingga sedang terhadap fitur lainnya, bahkan bernilai negatif.


## 4. Verifikasi Data

Berdasarkan hasil eksplorasi sebelumnya, diketahui bahwa dataset terdiri dari 150 data dengan jumlah yang konsisten pada setiap atribut numerik. Hasil pengecekan menggunakan df.isnull().sum() menunjukkan bahwa seluruh kolom memiliki nilai 0 pada bagian null, yang berarti tidak terdapat data kosong (missing value) pada dataset.

Selanjutnya, dilakukan pengecekan data duplikat menggunakan df.duplicated().sum(), dan diperoleh hasil sebanyak 3 data duplikat. Hal ini menunjukkan bahwa terdapat beberapa baris data yang identik dan berpotensi mempengaruhi hasil analisis jika tidak ditangani.

## 5. Visualisasi Data

### 5.1 Visualisasi data menggunakan Python

#### - Distribusi Jumlah kelas Iris

![original image](https://cdn.mathpix.com/snip/images/Gt4lANFXJ1tvApce6vXv0vo4hu08VXJb7_bTkbfgn-M.original.fullsize.png)

Grafik bar digunakan untuk melihat jumlah data pada setiap species. Berdasarkan grafik, diketahui bahwa setiap kelas species yaitu Iris-setosa, Iris-versicolor, dan Iris-virginica masing-masing memiliki 50 data. Hal ini menunjukkan bahwa dataset dalam kondisi seimbang (balanced dataset), sehingga tidak terdapat ketimpangan jumlah data antar kelas. Kondisi ini sangat baik untuk proses modeling karena dapat membantu menghasilkan model klasifikasi yang lebih akurat dan tidak bias terhadap kelas tertentu.

#### - Distribusi Data Fitur Numerik pada Dataset Iris

![original image](https://cdn.mathpix.com/snip/images/6u595jYczu5oHxd1yDMvEGkfvzQYsyvFrhOICQnLnGY.original.fullsize.png)

Berdasarkan histogram, dapat disimpulkan bahwa seluruh fitur numerik memiliki distribusi yang bervariasi dan tidak terdapat anomali yang ekstrem. Fitur petal_length dan petal_width menunjukkan pola distribusi yang lebih jelas dalam membedakan kelompok data, sehingga kedua fitur ini sangat berpotensi menjadi fitur penting dalam proses klasifikasi pada tahap modeling. Visualisasi ini membantu dalam memahami karakteristik dan penyebaran data pada tahap Data Understanding dalam metodologi CRISP-DM.

#### - Visualisasi scatter plot sepal dan petal
![original image](https://cdn.mathpix.com/snip/images/Y0D4Vc_bKZyv6KgPthBSm8pOPKFWZf-ZcZEN1qaBDpM.original.fullsize.png)

Scatter plot diatas menunjukan hubungan antara sepal_length pada sumbu X dan sepal_width pada sumbu Y. Pada visualisasi ini pola hubungan terlihat lebih menyebar dan tidak membentuk pola linear yang kuat. Penyebaran titik data cenderung acak dan tidak menunjukkan korelasi yang signifikan. Hal ini menunjukkan bahwa hubungan antara kedua fitur tersebut relatif lemah dibandingkan fitur petal. Dengan demikian, fitur sepal kurang efektif dalam membedakan species dibandingkan fitur petal.


![original image](https://cdn.mathpix.com/snip/images/cbXgTI0n7n5F-Hm_QEabnsoY06-1zrCTGJM6BGw0VT4.original.fullsize.png)

Scatter plot selanjutnya ini menunjukan hubungan antara petal_length pada sumbu X dan petal_width pada sumbu Y. Titik-titik data membentuk pola naik yang cukup linear, yang menunjukkan bahwa semakin panjang petal, maka semakin lebar petal. Selain itu, terlihat adanya pengelompokan data yang cukup jelas, yang mengindikasikan bahwa kedua fitur ini mampu membedakan species dengan baik. Hal ini mendukung hasil analisis korelasi yang menunjukkan bahwa petal_length dan petal_width memiliki nilai korelasi yang tinggi.


#### - Analisis Penyebaran Data dan Deteksi Outlier Menggunakan Boxplot

![original image](https://cdn.mathpix.com/snip/images/YfGQj5-WjGnpPQF6mWpdtoRSXTPrJIw5sXZ6l769Xo0.original.fullsize.png)
 
Pada setiap kotak:
- Garis di tengah kotak → Median (nilai tengah)
- Bagian bawah & atas kotak → Kuartil 1 (25%) dan Kuartil 3 (75%)
- Garis memanjang (whisker) → Rentang minimum dan maksimum

Berdasarkan boxplot, dapat disimpulkan bahwa setiap fitur memiliki penyebaran data yang berbeda. Fitur sepal_width menunjukkan adanya beberapa outlier, sedangkan fitur lainnya memiliki distribusi yang relatif normal tanpa outlier yang signifikan. Fitur petal_length dan petal_width memiliki variasi data yang cukup besar, sehingga berpotensi menjadi fitur penting dalam membedakan species pada tahap modeling.

### 5.2 Visualisasi data menggunakan Orange

#### - Statistik jumlah keseluruhan data
![original image](https://cdn.mathpix.com/snip/images/HDC--owJyN2brHw80LVv73mBWygr0yxk9lX4t96wFvc.original.fullsize.png)

#### - Visualisasi Bar Chart

![original image](https://cdn.mathpix.com/snip/images/0fNG62bZBym-M-49gaNRJ-AExLKqTm4Y6JaYxtxx_L0.original.fullsize.png)


![original image](https://cdn.mathpix.com/snip/images/5GkmBEAXfm44a2sSxu5ec27ANj-g9KUY8pKwPG6wNAU.original.fullsize.png)



#### - Visualisasi scatter plot sepal dan petal

![original image](https://cdn.mathpix.com/snip/images/wdF8Z79VGVflh2mDtT56v_4f5q5SlGbLHtM778Ut6ac.original.fullsize.png)

![original image](https://cdn.mathpix.com/snip/images/1K4uhfC_chCDhN56N1oE_UogCOe7cSdr_zdgA9iD8zI.original.fullsize.png)

#### - Visualisasi Box plot

![original image](https://cdn.mathpix.com/snip/images/0IzZzwtAwUWlcBUmvnfO6rYeciTHx-oNosmnHghUypU.original.fullsize.png)

#### - Analisa korelasi pada orange

![original image](https://cdn.mathpix.com/snip/images/5LdarTZMEcRto-WhHPhbKFnrNfQpFu_pgj1JCbCikyQ.original.fullsize.png)

## 6. Mengukur jarak data

### 6.1 Pengertian Similarity dan Dissimilarity

#### 6.1.1 Similarity
Sesuai namanya adalah kemiripan, dalam similarity yang diukur adalah mengukur seberapa mirip dua objek yang sedang disandingkan, semakin besar nilai yang dimiliki maka semakin mirip objek tersebut. Tinggi nilainya berada pada range [0,1]. Semakin tinggi nilai nya, maka semakin mirip objek yang dimaksud

#### 6.1.2 Dissimilarity
Terbanding terbalik dengan similarity, Disisimilarity mengukur seberapa berbeda dua objek yang sedang disandingkan. Nilai nya berawal dari 0, semakin besar nilai yang dimiliki, maka semakin tidak mirip objek tersebut.
Contoh Dissimilarity:
##### 6.1.2.1 Minkowski Distance
Minkowski Distance adalah bentuk generalisasi dari Euclidean dan Manhattan. Artinya Euclidean dan Manhattan adalah bagian dari kelompok
$$
\begin{aligned}
d(i,j) &= \sqrt[h]{|x_{i1}-x_{j1}|^{h} + |x_{i2}-x_{j2}|^{h} + \cdots + |x_{ip}-x_{jp}|^{h}} \\
\\
\text{dengan } \quad
i &= (x_{i1}, x_{i2}, \dots, x_{ip}), \\
j &= (x_{j1}, x_{j2}, \dots, x_{jp})
\end{aligned}
$$
i = Melambangkan objek ke-i
j = Melambangkan objek ke-j
xi1 = Nilai fitur 1 dari objek ke-i
xj1 = Nilai fitur 1 dari objek ke-j

##### 6.1.2.2 Manhattan Distance
Manhattan Distance mengukur seberapa jauh jarak antara 2 titik dengan menjumlahkan selisih absolut dari koordinatnya. Manhattan distance lebih memliki ketahanan terhadap outlier dibandingkan dengan euclidean distance. Manhattan distance cocok untuk data yang berdimensi tinggi
$$
d(i,j) = |x_{i1} - x_{j1}| + |x_{i2} - x_{j2}| + \cdots + |x_{ip} - x_{jp}|
$$
##### 6.1.2.3 Euclidean Distance
Yang terakhir ada Euclidean Distance, Euclidean digunakan untuk data bertipe numerik meskipun jarak Euclidean sangat umum dalam pengelompokan. Salah satu permasalahan yang ada dalam Euclidean adalahjarak Euclidean sebagai fitur skala terbesar akan mendominasi yang lain. Normalisasi fitur kontinu adalah solusi untuk mengatasi kelemahan ini.

$d(i,j) = \sqrt{(x_{i1} - x_{j1}|^2 +| x_{i2} - x_{j2}|^2 + \ldots + |x_{ip} - x_{jp}|^2})$

### 6.2 Mengukur jarak dissimilarity pada suatu dataset

#### 6.2.1 Mengukur jarak dataset Iris
Pada dataset Iris diatas, tipe data yang dimiliki adalah bernilai numerik, karena hal itu penghitungan jarak untuk tipe data Iris kali ini akan digunakan metode Eucledian Distance
##### 6.2.1.1 Perhitungan manual 
Contoh perhitungan secara manual pada baris 2 dan 3 dengan kolom 1:
$$
d(2,1) = \sqrt{(
|4.9 - 5.1|^2 +
|3 - 3.5|^2 +
|1.4 - 1.4|^2 +
|0.2 - 0.2|^2
)}
$$
$$
= \sqrt{0.04 + 0.25}
$$

$$
= 0.538
$$
$$
d(3,1) = \sqrt{(
|4.7 - 5.1|^2 +
|3.2 - 3.5|^2 +
|1.3 - 1.4|^2 +
|0.2 - 0.2|^2
)}
$$

$$
= \sqrt{0.16 + 0.09 + 0.01 + 0}
$$

$$
= 0.50990
$$
##### 6.2.1.2 Perhitungan Python
Kita juga dapat menggunakan tools seperti python untuk mempermudah perhitungan ini
```
from scipy.spatial.distance import pdist, squareform

X = df.select_dtypes(include=['float64', 'int64'])
dist = pdist(X, metric='euclidean')
distance_matrix = squareform(dist)

distance_df = pd.DataFrame(distance_matrix)
print(distance_df.iloc[:5, :5])
```
Terdapat tambahan library yang digunakan yaitu library scipy untuk mempermudah kita menghitung Euclidean Distance tanpa perlu coding manual. Selanjutnya data ditampilkan hanya sebesar 5x5 dihitung dari yang pertama

Output yang dihasilkan:
|   | 0        | 1        | 2        | 3        | 4        |
|---|----------|----------|----------|----------|----------|
| 0 | 0.000000 | 0.538516 | 0.509902 | 0.648074 | 0.141421 |
| 1 | 0.538516 | 0.000000 | 0.300000 | 0.331662 | 0.608276 |
| 2 | 0.509902 | 0.300000 | 0.000000 | 0.244949 | 0.509902 |
| 3 | 0.648074 | 0.331662 | 0.244949 | 0.000000 | 0.648074 |
| 4 | 0.141421 | 0.608276 | 0.509902 | 0.648074 | 0.000000 |

##### 6.2.1.3 Perhitungan Orange
![original image](https://cdn.mathpix.com/snip/images/9PGgc7ny0uksXf9OeJWUUqJ1W74_wYqqPmGLGPGhBL8.original.fullsize.png)
3 perhitungan diatas, memiliki kesamaan nilai, artinya perhitungan yang dilakukan sudah dilakukan secara benar dan urut

#### 6.2.2 Mengukur jarak dataset Tipe data campuran
Dataset yang digunakan untuk contoh kali ini adalah dataset berjudu; 'Student Alcohol Consumption'. Dimana dataset ini memiliki sekitar 30 fitur di dalam nya. Namun pada penugasan kali ini, kami menggunakan hanya 7 fitur diantaranya yaitu fitur sex, age, Medu, Fedu, Fjob, activities, schoolsup. Dimana tipe data Binary dimiliki oleh [sex, activities, schoolsup] lalu tipe data numerik [Age] dan tipe data kategorikal [fjob] terakhir adalah tipe data ordinal [medu, fedu].
[Dataset Student alcohol consumption](https://www.kaggle.com/datasets/uciml/student-alcohol-consumption)
##### 6.2.2.1 Perhitungan manual 
Contoh perhitungan manual antara baris 1 dan 2

###### 1. Hitung ordinal dan numerik pada baris 1 dan 2 lalu 1 dan 3

Untuk menghitung tipe data ordinal kita perlu melakukan normalisasi terhadap data tersebut dengan rumus

$$
x' = \frac{r - r_{min}}{r_{max} - r_{min}}
$$

Selanjutnya masing masing nilai yang mewakili kolom nya akan di normalisasi

$$
1 \rightarrow \frac{1-1}{4-1} = 0
$$

$$
2 \rightarrow \frac{2-1}{4-1} = \frac{1}{3} = 0,333
$$

$$
3 \rightarrow \frac{3-1}{4-1} = \frac{2}{3} = 0,666
$$

$$
4 \rightarrow \frac{4-1}{4-1} = 1
$$

Hasil normalisasi diatas akan menggantikan setiap nilai pada kolom **medu** dan **fedu**, sebagai contoh nilai **medu 2** maka akan digantikan dengan **0,333**.

Selanjutnya, kita akan melakukan perhitungan untuk data \(d(1,2)\) dan \(d(1,3)\) untuk metode ordinal, dan pada tahap ini perhitungan untuk data numerik juga akan digabung dengan metode **euclidean distance**.

$$
d(1,2) = \sqrt{(0-1)^2 + (0-1)^2 + (17-18)^2} = \sqrt{3} = 1,732
$$

$$
d(1,3) = \sqrt{(0-1)^2 + (0-1)^2 + (15-18)^2} = \sqrt{1+1+9} = \sqrt{11} = 3,316
$$


###### 2. Hitung Binary pada baris 1 dan 2 lal baris 1 dan 3

Pertama kita perlu mengidentifikasi, apakah data binary yang kita miliki simetris atau asimetris. Cara untuk mengidentifikasi nya adalah ketika data tersebut tidak memiliki tingkatan, maka dia simetris sebagai contoh **gender**. Jika data tersebut memiliki tingkatan maka dia asimetris sebagai contoh **penyakit**.

Untuk rumus simetris adalah sebagai berikut:

$$
\frac{r+s}{q+r+s+t}
$$

Sedangkan rumus asimetris:

$$
\frac{r+s}{q+r+s}
$$

Selanjutnya kita tentukan kolom apa saja yang simetris dan asimetris. **Sex** masuk sebagai simetris sedangkan **activities** dan **schoolsup** asimetris.

Maka perhitungan kolom **sex** pada \(d(1,2)\) dilakukan dengan:

$$
q = 0
$$

$$
r = 0
$$

$$
s = 0
$$

$$
t = 1
$$

$$
\frac{0+0}{0+0+0+1} = 0
$$

Sedangkan kolom **activities** dan **schoolsup** pada \(d(1,2)\):

$$
q = 0
$$

$$
r = 1
$$

$$
s = 0
$$

$$
t = 1
$$

$$
\frac{1+0}{0+1+0} = 1
$$

Perhitungan kolom **sex** pada \(d(1,3)\):

$$
q = 0
$$

$$
r = 0
$$

$$
s = 0
$$

$$
t = 1
$$

$$
\frac{0+0}{0+0+0+1} = 0
$$

Sedangkan kolom **activities** dan **schoolsup** pada \(d(1,3)\):

$$
q = 1
$$

$$
r = 0
$$

$$
s = 0
$$

$$
t = 1
$$

$$
\frac{0+0}{1+0+0} = 0
$$

Hasil akhir \(d(1,2)\) dan \(d(1,3)\):

$$
d(1,2) = 1
$$

$$
d(1,3) = 0
$$


###### 3. Hitung Kategorikal pada baris 1 dan 2 dan baris 1 dan 3

Untuk melakuka perhitungan kategori, ada beberapa hal yang perlu diperhatikan, pertama adalah jumlah fitur yang dimiliki, kedua adalah data yang sama yang sedang dibandingkan, maka rumusnya dapat ditulis sebagai berikut:

$$
\frac{p-m}{p}
$$

$$
p = 1
$$

P adalah jumlah fitur, dimana di kasus ini fitur nya hanya **fjob**.

Perhitungan nilai kategori \(d(1,2)\):

$$
d(1,2) = \frac{1-0}{1} = 1
$$

Perhitungan nilai kategori \(d(1,3)\):

$$
d(1,3) = \frac{1-0}{1} = 1
$$


###### 3. Hasil Akhir perhitungan baris 1 dan 2 lalu baris 1 dan 3

Nilai dari hasil perhitungan sebelumnya akan kita jumlahkan seluruhnya

$$
d(1,2) = 1,732 + 0 + 1 + 1 = 3,732
$$

$$
d(1,3) = 3,316 + 0 + 0 + 1 = 4,316
$$