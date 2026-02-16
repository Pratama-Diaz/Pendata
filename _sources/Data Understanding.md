# Data Understanding
### 1. Sumber Data

Dataset yang digunakan dalam penelitian ini adalah Iris Flower Dataset yang diperoleh dari platform Kaggle. Dataset ini berisi data pengukuran morfologi bunga iris yang terdiri dari tiga spesies, yaitu Iris-setosa, Iris-versicolor, dan Iris-virginica.
[Dataset Iris](https://www.kaggle.com/datasets/arshid/iris-flower-dataset?resource=download)


### 2. Eksplorasi Dataset

Dalam proses mengidentifikasi dataset ini, python membantu untuk mempermudah pengidentifikasiannya.

##### - Persiapan library dan perizinan drive

```
from google.colab import drive
import pandas as pd
import matplotlib.pyplot as plt

drive.mount('/content/drive')
```

Pengizinan pembacaan file dalam google drive, ini digunakan agar google collaboratory dapat membaca file IRIS.csv yang kita taruh di dalam google drive

##### - Menentukan path drive

```
path = "/content/drive/MyDrive/tugas/IRIS.csv"
df = pd.read_csv(path)
```

Dalam kasus ini file IRIS.csv berada dalam folder tugas, maka path kita arahkan menuju folder tugas, lalu gunakan library pandas untuk membaca isi file nya

##### - Struktur Dataset

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

##### -Statistik Deskriptif Awal

```
df.describe()
```

Berikut contoh statistik deskriptifnya:


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

 Berdasarkan hasil statistik deskriptif, dataset Iris terdiri dari 150 data pada setiap atribut. Nilai rata-rata menunjukkan bahwa ukuran sepal dan petal berada pada rentang yang wajar, dengan panjang sepal rata-rata sekitar 5.84 cm dan panjang petal sekitar 3.76 cm. Variasi data terbesar terdapat pada atribut petal length, sedangkan variasi terkecil terdapat pada sepal width, yang menunjukkan bahwa ukuran petal lebih beragam dibandingkan ukuran sepal. Rentang nilai minimum hingga maksimum pada setiap atribut masih berada dalam batas yang normal dan tidak menunjukkan adanya nilai ekstrem yang mencurigakan. 
 
 Standar deviasi tertinggi terdapat pada atribut petal length sebesar 1.76, yang menunjukkan bahwa variasi panjang petal lebih besar dibandingkan atribut lainnya. Nilai minimum dan maksimum menunjukkan rentang data yang masih dalam batas wajar, yaitu sepal length antara 4.3 hingga 7.9 cm dan petal width antara 0.1 hingga 2.5 cm. Selain itu, nilai kuartil (25%, 50%, dan 75%) menunjukkan distribusi data yang relatif stabil tanpa adanya penyimpangan ekstrem yang signifikan.
 
##### - Pengecekan Data Duplikat

```
df.duplicated().sum()
```

Code di atas merupakan code untuk pengecekan data duplikat, berikut untuk hasil dari pengecekan data duplikat:

```
np.int64(3)
```

Jadi setelah dilakukan pengecekan, terdapat data duplikat yaitu ada 3 data duplikat.

##### - Pengecekan Data Null

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

### 3. Verifikasi Data

Berdasarkan hasil eksplorasi sebelumnya, diketahui bahwa dataset terdiri dari 150 data dengan jumlah yang konsisten pada setiap atribut numerik. Hasil pengecekan menggunakan df.isnull().sum() menunjukkan bahwa seluruh kolom memiliki nilai 0 pada bagian null, yang berarti tidak terdapat data kosong (missing value) pada dataset.

Selanjutnya, dilakukan pengecekan data duplikat menggunakan df.duplicated().sum(), dan diperoleh hasil sebanyak 3 data duplikat. Hal ini menunjukkan bahwa terdapat beberapa baris data yang identik dan berpotensi mempengaruhi hasil analisis jika tidak ditangani.

### 4. Visualisasi Data

##### - Distribusi Jumlah Data per Species

![original image](https://cdn.mathpix.com/snip/images/Gt4lANFXJ1tvApce6vXv0vo4hu08VXJb7_bTkbfgn-M.original.fullsize.png)

Grafik bar digunakan untuk melihat jumlah data pada setiap species. Berdasarkan grafik, diketahui bahwa setiap species yaitu Iris-setosa, Iris-versicolor, dan Iris-virginica masing-masing memiliki 50 data. Hal ini menunjukkan bahwa dataset dalam kondisi seimbang (balanced dataset), sehingga tidak terdapat ketimpangan jumlah data antar kelas. Kondisi ini sangat baik untuk proses modeling karena dapat membantu menghasilkan model klasifikasi yang lebih akurat dan tidak bias terhadap kelas tertentu.

##### - Distribusi Data Fitur Numerik pada Dataset Iris

![original image](https://cdn.mathpix.com/snip/images/6u595jYczu5oHxd1yDMvEGkfvzQYsyvFrhOICQnLnGY.original.fullsize.png)

Berdasarkan histogram, dapat disimpulkan bahwa seluruh fitur numerik memiliki distribusi yang bervariasi dan tidak terdapat anomali yang ekstrem. Fitur petal_length dan petal_width menunjukkan pola distribusi yang lebih jelas dalam membedakan kelompok data, sehingga kedua fitur ini sangat berpotensi menjadi fitur penting dalam proses klasifikasi pada tahap modeling. Visualisasi ini membantu dalam memahami karakteristik dan penyebaran data pada tahap Data Understanding dalam metodologi CRISP-DM.

##### - Analisis Penyebaran Data dan Deteksi Outlier Menggunakan Boxplot

![original image](https://cdn.mathpix.com/snip/images/YfGQj5-WjGnpPQF6mWpdtoRSXTPrJIw5sXZ6l769Xo0.original.fullsize.png)
 
Pada setiap kotak:
- Garis di tengah kotak → Median (nilai tengah)
- Bagian bawah & atas kotak → Kuartil 1 (25%) dan Kuartil 3 (75%)
- Garis memanjang (whisker) → Rentang minimum dan maksimum

Berdasarkan boxplot, dapat disimpulkan bahwa setiap fitur memiliki penyebaran data yang berbeda. Fitur sepal_width menunjukkan adanya beberapa outlier, sedangkan fitur lainnya memiliki distribusi yang relatif normal tanpa outlier yang signifikan. Fitur petal_length dan petal_width memiliki variasi data yang cukup besar, sehingga berpotensi menjadi fitur penting dalam membedakan species pada tahap modeling.