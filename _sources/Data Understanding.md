# Data Understanding

### 1. Sumber Data

Dataset yang digunakan adalah Iris Flower Dataset yang diperoleh dari platform Kaggle. Dataset ini merupakan dataset klasik dalam bidang data mining dan machine learning yang berisi data pengukuran bunga iris dari tiga spesies berbeda, yaitu Setosa, Versicolor, dan Virginica. Dataset ini digunakan sebagai studi kasus untuk penerapan metode klasifikasi dalam penambangan data.
Berikut link dari dataset
[Dataset Iris](https://www.kaggle.com/datasets/arshid/iris-flower-dataset?resource=download)

### 2. Deskripsi Dataset

Dataset yang digunakan dalam penelitian ini adalah Iris Flower Dataset yang terdiri dari 150 data dengan 5 atribut. Dataset ini digunakan untuk mengklasifikasikan bunga iris ke dalam tiga spesies berdasarkan ukuran bagian bunganya.

### 3. Eksplorasi Dataset

Dalam proses mengidentifikasi dataset ini, python membantu untuk mempermudah pengidentifikasiannya.

##### - Upload file CSV

```
from google.colab import files
files.upload()
```

Proses upload file CSV dilakukan untuk memasukkan dataset ke dalam lingkungan Google Colaboratory agar dapat diproses menggunakan bahasa pemrograman Python. Dataset diunggah secara manual dari perangkat pengguna menggunakan fungsi files.upload() yang disediakan oleh Google Colab.

##### - Struktur Dataset

```
import pandas as pd
import matplotlib.pyplot as plt

df = pd.read_csv("IRIS.csv")
df.head()
```

Berikut tampilan dari code di atas setelah di jalankan:


| index | sepal\_length | sepal\_width | petal\_length | petal\_width | species |
| :-- | :-- | :-- | :-- | :-- | :-- |
| 0 | 5\.1 | 3\.5 | 1\.4 | 0\.2 | Iris-setosa |
| 1 | 4\.9 | 3\.0 | 1\.4 | 0\.2 | Iris-setosa |
| 2 | 4\.7 | 3\.2 | 1\.3 | 0\.2 | Iris-setosa |
| 3 | 4\.6 | 3\.1 | 1\.5 | 0\.2 | Iris-setosa |
| 4 | 5\.0 | 3\.6 | 1\.4 | 0\.2 | Iris-setosa |

Dataset memiliki 5 kolom, yaitu:

1. Sepal Length (cm) (Numeric)
Menunjukkan panjang kelopak luar bunga iris dalam satuan sentimeter.
2. Sepal Width (cm) (Numeric)
Menunjukkan lebar kelopak luar bunga iris dalam satuan sentimeter.
3. Petal Length (cm) (Numeric)
Menunjukkan panjang mahkota bunga iris dalam satuan sentimeter.
4. Petal Width (cm) (Numeric)
Menunjukkan lebar mahkota bunga iris dalam satuan sentimeter.
5. Species (Kategorikal - String)
Menunjukkan jenis bunga iris, yang terdiri dari tiga kategori:
    - Setosa
    - Versicolor
    - Virginica

##### -Statistik Deskriptif Awal

```
df.describe()
```

Statistik deskriptif digunakan untuk mengetahui gambaran umum data seperti nilai rata-rata, nilai minimum, dan maksimum dari setiap atribut numerik.

Dataset Iris memiliki 4 atribut numerik, yaitu:

- Sepal Length
- Sepal Width
- Petal Length
- Petal Width

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

Berdasarkan hasil analisis statistik deskriptif, dataset Iris terdiri dari 150 data pada setiap atribut numerik, sehingga dapat disimpulkan tidak terdapat missing value. Nilai rata-rata (mean) menunjukkan bahwa panjang sepal bunga iris adalah 5.84 cm, lebar sepal 3.05 cm, panjang petal 3.76 cm, dan lebar petal 1.20 cm. Standar deviasi tertinggi terdapat pada atribut petal length sebesar 1.76, yang menunjukkan bahwa variasi panjang petal lebih besar dibandingkan atribut lainnya. Nilai minimum dan maksimum menunjukkan rentang data yang masih dalam batas wajar, yaitu sepal length antara 4.3 hingga 7.9 cm dan petal width antara 0.1 hingga 2.5 cm. Selain itu, nilai kuartil (25%, 50%, dan 75%) menunjukkan distribusi data yang relatif stabil tanpa adanya penyimpangan ekstrem yang signifikan. Secara umum, dataset memiliki distribusi yang baik dan layak digunakan untuk proses analisis dan pemodelan pada tahap selanjutnya.

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

### 4. Verifikasi Data

Berdasarkan proses verifikasi, dataset Iris terdiri dari 150 data dengan 4 atribut numerik dan 1 atribut kategorikal (species). Hasil pengecekan menunjukkan bahwa seluruh atribut memiliki jumlah data yang sama (count = 150), sehingga dapat disimpulkan tidak terdapat missing value pada dataset. Tipe data juga telah sesuai, di mana atribut sepal length, sepal width, petal length, dan petal width bertipe numerik, sedangkan species bertipe kategorikal. Berdasarkan hasil pengecekan menggunakan Pandas, ditemukan sebanyak 3 data duplikat pada dataset. Keberadaan data duplikat ini tidak menunjukkan kesalahan pada dataset, namun perlu ditangani pada tahap data preparation agar tidak memengaruhi proses pemodelan.

### 5. Visualisasi Data

##### - Distribusi Jumlah Data per Species

![original image](https://cdn.mathpix.com/snip/images/Gt4lANFXJ1tvApce6vXv0vo4hu08VXJb7_bTkbfgn-M.original.fullsize.png)

Grafik bar digunakan untuk melihat jumlah data pada setiap species. Berdasarkan grafik, diketahui bahwa setiap species yaitu Iris-setosa, Iris-versicolor, dan Iris-virginica masing-masing memiliki 50 data. Hal ini menunjukkan bahwa dataset dalam kondisi seimbang (balanced dataset), sehingga tidak terdapat ketimpangan jumlah data antar kelas. Kondisi ini sangat baik untuk proses modeling karena dapat membantu menghasilkan model klasifikasi yang lebih akurat dan tidak bias terhadap kelas tertentu.

##### - Distribusi Data Fitur Numerik pada Dataset Iris

![original image](https://cdn.mathpix.com/snip/images/6u595jYczu5oHxd1yDMvEGkfvzQYsyvFrhOICQnLnGY.original.fullsize.png)

Berdasarkan histogram, dapat disimpulkan bahwa seluruh fitur numerik memiliki distribusi yang bervariasi dan tidak terdapat anomali yang ekstrem. Fitur petal_length dan petal_width menunjukkan pola distribusi yang lebih jelas dalam membedakan kelompok data, sehingga kedua fitur ini sangat berpotensi menjadi fitur penting dalam proses klasifikasi pada tahap modeling. Visualisasi ini membantu dalam memahami karakteristik dan penyebaran data pada tahap Data Understanding dalam metodologi CRISP-DM.

##### - Analisis Penyebaran Data dan Deteksi Outlier Menggunakan Boxplot

![original image](https://cdn.mathpix.com/snip/images/YfGQj5-WjGnpPQF6mWpdtoRSXTPrJIw5sXZ6l769Xo0.original.fullsize.png)

Berdasarkan boxplot, dapat disimpulkan bahwa setiap fitur memiliki penyebaran data yang berbeda. Fitur sepal_width menunjukkan adanya beberapa outlier, sedangkan fitur lainnya memiliki distribusi yang relatif normal tanpa outlier yang signifikan. Fitur petal_length dan petal_width memiliki variasi data yang cukup besar, sehingga berpotensi menjadi fitur penting dalam membedakan species pada tahap modeling. Visualisasi boxplot ini membantu dalam memahami distribusi data dan mendeteksi outlier pada tahap Data Understanding dalam metodologi CRISP-DM.

