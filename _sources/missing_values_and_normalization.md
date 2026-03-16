# Missing Values Inputation dan normalisasi data
## 1. Missing values inputation denga metode WKNN
Missing values adalah suatu kondisi dimana dataset yang dimiliki memiliki nilai atribut yang tidak tersedia atau kosong. Hal itu akan memengaruhi kualitas suatu data, sehingga ada metode yang dapat digunakan untuk mengisi atau lebih tepatnya memperkirakan untuk mengisi missing values tersebut. Salah satu metode yang dapat digunakan adalah Weighted K-Nearest Neighbour (WKNN).

Metode WKNN merupakan pengembangan dari metode K-Nearest Neighbour (KNN) yang digunakan untuk melakukan imputasi nilai yang hilang berdasarkan kemiripan antar data. Pada metode ini nilai yang hilang diperkirakan denggan tetangga terdekat (nearest neighbours) yang memiliki kemiripan tertinggi. Perbedaan utama antara KNN dengan WKNN terletak pada pemberian bobot untuk setiap tetangga, dalam WKNN data yang jaraknya dekat akan diberikan bobot lebih besar dibandingkan jarak yang jauh.

### Langkah-langkah melakukan metode WKNN:
1. Mengidentifikasi missing values pada data
2. Melakukan normalisasi data
3. Menghitung similarity antar data
4. Menentukan beberapa tetangga terdekat (K tetangga)
5. Menghitung nilai imputasi

#### Rumus normalisasi data (min-max method)
$$
x' = \frac{x - x_{min}}{x_{max} - x_{min}}
$$
Keterangan:
- $x$ : nilai data asli  
- $x_{min}$ : nilai minimum pada atribut  
- $x_{max}$ : nilai maksimum pada atribut  
- $x'$ : nilai hasil normalisasi

#### Rumus Similarity pada WKNN
$$
S_i = \frac{1}{\sum (Y_{ih} - Y_{jh})^2}
$$
Keterangan:
- $S_i$ : nilai similarity antara data ke-$i$ dan data ke-$j$  
- $Y_{ih}$ : nilai atribut ke-$h$ pada data yang memiliki missing value  
- $Y_{jh}$ : nilai atribut ke-$h$ pada data tetangga  

#### Rumus perhitungan nilai imputasi
$$
\hat{y}_{ih} =
\frac{\sum_{j \in I_{kih}} s_i(y_j)\, y_{jh}}
{\sum_{j \in I_{kih}} s_i(y_j)}
$$
Keterangan:

- $\hat{y}_{ih}$ : nilai imputasi pada atribut ke-$h$ dari data ke-$i$  
- $I_{kih}$ : himpunan indeks dari $k$ tetangga terdekat  
- $s_i(y_j)$ : nilai similarity antara data $i$ dan data tetangga $j$  
- $y_{jh}$ : nilai atribut ke-$h$ dari data tetangga ke-$j$

Perlu diingat dalam metode ini, penentuan tetangga terdekat tidak ada metode pasti atau jumlah pasti yang digunakan, semuanya tergantung kebutuhan dataset tersebut.

### Dataset yang digunakan
Dataset yang digunakan berasal dari platform kaggle mengenai Red wine quality, dapat diakses di link berikut: [Dataset Red wine quality](https://www.kaggle.com/datasets/uciml/red-wine-quality-cortez-et-al-2009)

Dataset Red Wine Quality merupakan dataset yang berisi informasi mengenai kualitas anggur merah berdasarkan berbagai karakteristik kimia. Dataset ini berasal dari penelitian yang dilakukan oleh Paulo Cortez. Dataset ini berisi data dari varian anggur merah Portugis yang dikenal sebagai Vinho Verde. Setiap baris data merepresentasikan satu sampel anggur yang diuji. Terdapat sekitar 1599 data di dalamnya, dengan 11 fitur dan 1 class.

#### Penjelasan masing-masing fitur dan class:
| Column | Deskripsi |
|---|---|
| fixed acidity | jumlah asam tetap dalam anggur yang tidak mudah menguap |
| volatile acidity | jumlah asam asetat yang dapat menyebabkan rasa seperti cuka jika terlalu tinggi |
| citric acid | kandungan asam sitrat yang dapat memberikan rasa segar pada anggur |
| residual sugar | jumlah gula yang tersisa setelah proses fermentasi |
| chlorides | jumlah kandungan garam dalam anggur |
| free sulfur dioxide | jumlah sulfur dioksida bebas yang berfungsi mencegah oksidasi dan pertumbuhan mikroba |
| total sulfur dioxide | total kandungan sulfur dioksida bebas dan terikat dalam anggur |
| density | kepadatan cairan anggur yang dipengaruhi oleh kadar gula dan alkohol |
| pH | tingkat keasaman anggur |
| sulphates | senyawa yang berperan sebagai antimikroba dan antioksidan |
| alcohol | kadar alkohol dalam anggur |
| quality | skor kualitas anggur yang diberikan oleh panel ahli dengan rentang nilai 0–10 |

### Perhitungan WKNN secara manual
#### 1. Menentukan missing values 
Pada dataset ini digunaka contoh pada kolom sulphates di baris ke 3 data memiliki missing values, tugas kita adalah mengisi missing values tersebut menggunakan metode WKNN ini

#### 2. Melakukan normalisasi data
Selanjutnya untuk menghitung similarity diperlukan normalisasi data, agar data, hal ini dilakukan untuk menghindari data yang memiliki nilai sangat jauh. Normalisasi pada data ini akan digunakan metode min-max normalization. Tentukan kolom yang akan dinormalisasi. Pada kasus ini akan ada 4 data dengan class yang sama yang akan dilakukan normalisasi

$$
x' = \frac{x - x_{min}}{x_{max} - x_{min}}
$$

![original image](https://cdn.mathpix.com/snip/images/UBpHG5YqHVp_-vpcf1AxRNKpSO-RXqzRSl6Xqv60rE4.original.fullsize.png)

Pada kolom diatas terlihat bahwasan nya pada baris ke 3 kolom sulphates memiliki missing values, selanjutnya kolom seperti quality tidak digunakan karena kolom tersebut merupakan class. 
selanjutnya untuk melakukan normalisasi perlu menentukan nilai min dan max setiap kolom, hasilnya:

![original image](https://cdn.mathpix.com/snip/images/XadqnpAdHx_IpPlmjPfLa0CPTSQ3et4ZN3TxSUXSY_M.original.fullsize.png)
Baris 1 adalah nilai min, baris 2 adalah nilai max

Contoh perhitungan normalisasi:

$$
x' = \frac{7.4 - 7.4}{7.8 - 7.4} = 0
$$

Hasil diatas merupakan contoh perhitungan normalisasi untuk baris 1 kolom 1, lakukan terhadap semuanya 
Hasil akhir:
![original image](https://cdn.mathpix.com/snip/images/UWukQP7caBcXZ1_w7TJRoDZHf_gExp88MPlEoEedmd4.original.fullsize.png)

#### 3. Menghitung nilai similarity
Setelah melakukan penormalisasian data, yang dilakukan selanjutnya adalah menghitung nilai similarity (Si). Yang dihitung adalah baris data yang memiliki missing values, dengan baris lain nya yang tidak ada missing values

$$
S_i = \frac{1}{\sum (Y_{ih} - Y_{jh})^2}
$$

Contoh perhitungan similarity pada baris 3 dan 1:
$$
\frac{1}{
(1-0)^2 +
(0.333333333-0)^2 +
(1-0)^2 +
(0.571428571-0)^2 +
(0.727272727-0)^2 +
(0.285714286-0)^2 +
(0.606060606-0)^2 +
(0.2-1)^2 +
(0.193548387-1)^2 +
(1-0)^2
= 0,17526
}
$$
Lakukan semua perhitungan tersebut ke baris 2 dan 4, maka hasil yang diperoleh adalah sebagai berikut:
| Si |
| :-- |
| 0.175257999
 |
| 0.408939184
 |
| ? |
| 0.175257999
 |
#### 4. Menghitung penyebut 
Dari hasil similarity sebelumnya, kita bisa mendapatkan nilai penyebut dengan menjumlahkan semua hasil Similarity sebelumnya (kecuali yang memiliki missing values). Sehingga hasilnya akan menjadi
$$
0.175257999 + 0.408939184 + 0.175257999 = 0.759455182
$$
Maka nilai 0.759455182 akan menjadi penyebut nantinya

#### 5. Menghitung pembilang
Untuk mendapatkan nilai pembilang, hal selanjutnya yang perlu dilakukan adalah menghitung nilai similarity yang dimiliki dengan nilai atribut dari tetangga terdekat
$$
S_i \times Y_{jh}
$$
| sulphates ($Y_{jh}$) | $S_i$ | $S_i \times Y_{jh}$ |
|---|---|---|
| 0 | 0.175257999 | 0 |
| 1 | 0.408939184 | 0.408939184 |
| 0 | 0.175257999 | 0 |
Hasil akhir nilai pembilang adalah 0.408939184
#### 6. Menghitung hasil nilai imputasi
Untuk mendapatkan nilai imputasi, kita hanya perlu membagi antara pembilang dan penyebut nya
$$
\hat{y}_{ih} =
\frac{0.408939184}{0.759455182}
$$

$$
\hat{y}_{ih} = 0.53846388
$$
Nilai **0.53846388** ini akan menggantikan nilai missing values pada atribut sulphates

### Perhitungan WKNN menggunaka python
Dalam melakukan pengisian missing values dengan metode WKNN selain dilakukan perhitungan secara manual, dan menggunakan excel, kita bisa melakukan nya dengan tools yang lebih expert dan praktis, python menjawab persoalan ini dengan tepat
Langkah langkah untuk code python nya:

#### 1. Setting library dan akses file dalam drive
```
from google.colab import drive
import pandas as pd
import numpy as np
from sklearn.preprocessing import MinMaxScaler
from sklearn.metrics import pairwise_distances

drive.mount('/content/drive')
```
- import drive : Pengizinan pembacaan file dalam google drive, ini digunakan agar google collaboratory dapat membaca file yang kita taruh di dalam google drive.
- import pandas : Digunakan untuk menganalisis dan memanipulasi data yang dimiliki
- import numpy : import pandas : Digunakan untuk menganalisis dan memanipulasi data yang dimiliki
- import MinMaxScaler from library sklearn : digunakan untuk melakukan normalisasi data dengan metode min-max scaling sehingga nilai setiap fitur berada pada rentang 0 sampai 1
- import pairwise_distances from library sklearn : Digunakan untuk menghitung jarak antar data (Euclidean distance) antara dua baris atau lebih, yang diperlukan dalam proses menghitung similarity

#### 2. Menentukan path drive dan melihat beberapa data teratas
```
path = "/content/drive/MyDrive/tugas/WineQualityAssignment.csv"
df = pd.read_csv(path)
df.head()
```
Output
|index|fixed acidity|volatile acidity|citric acid|residual sugar|chlorides|free sulfur dioxide|total sulfur dioxide|density|pH|sulphates|alcohol|quality|
|---|---|---|---|---|---|---|---|---|---|---|---|---|
|0|7\.4|0\.7|0\.0|1\.9|0\.076|11|34|0\.9978|3\.51|0\.56|9\.4|5|
|1|7\.8|0\.88|0\.0|2\.6|0\.098|25|67|0\.9968|3\.2|0\.68|9\.8|5|
|2|7\.8|0\.76|0\.04|2\.3|0\.092|15|54|0\.997|3\.26|NaN|9\.8|5|
|3|7\.4|0\.7|0\.0|1\.9|0\.076|11|34|0\.9978|3\.51|0\.56|9\.4|5|

#### 3. Menentukan missing values 
```
missing_pos = np.where(df.isna())

row_missing = missing_pos[0][0]
col_missing = missing_pos[1][0]

missing_column = df.columns[col_missing]

print("Missing value ditemukan pada:")
print("Baris :", row_missing)
print("Kolom :", missing_column)
```
Dengan menggunaka bantuan library pandas dan numpy kita bisa menentukan missing values berada pada bagian mana

Output
```
Missing value ditemukan pada:
Baris : 2
Kolom : sulphates
```
#### 4. Mengetahui nilai Min-Max masing masing kolom

```
min_values = df[features].min()
max_values = df[features].max()

min_max_table = pd.DataFrame([min_values, max_values], index=["min", "max"])

print("Nilai Min dan Max setiap kolom:")
min_max_table
```
Output:
|index|fixed acidity|volatile acidity|citric acid|residual sugar|chlorides|free sulfur dioxide|total sulfur dioxide|density|pH|alcohol|
|---|---|---|---|---|---|---|---|---|---|---|
|min|7\.4|0\.7|0\.0|1\.9|0\.076|11\.0|34\.0|0\.9968|3\.2|9\.4|
|max|7\.8|0\.88|0\.04|2\.6|0\.098|25\.0|67\.0|0\.9978|3\.51|9\.8|

#### 5. Normalisasi data
Lakukan normalisasi data dengan library panda dan scikit
```
df_norm = df.copy()

cols_to_normalize = df.columns.drop(class_col)

scaler = MinMaxScaler()

df_norm[cols_to_normalize] = scaler.fit_transform(df[cols_to_normalize])

print("Data setelah normalisasi:")
df_norm
```
Output:
|index|fixed acidity|volatile acidity|citric acid|residual sugar|chlorides|free sulfur dioxide|total sulfur dioxide|density|pH|sulphates|alcohol|quality|
|---|---|---|---|---|---|---|---|---|---|---|---|---|
|0|0\.0|0\.0|0\.0|0\.0|0\.0|0\.0|0\.0|1\.0|1\.0|0\.0|0\.0|5|
|1|1\.0|1\.0000000000000004|0\.0|1\.0|1\.0000000000000004|0\.9999999999999999|1\.0|0\.0|0\.0|1\.0|1\.0|5|
|2|1\.0|0\.3333333333333335|1\.0|0\.5714285714285712|0\.7272727272727271|0\.2857142857142857|0\.6060606060606062|0\.1999999999999318|0\.19354838709677402|NaN|1\.0|5|
|3|0\.0|0\.0|0\.0|0\.0|0\.0|0\.0|0\.0|1\.0|1\.0|0\.0|0\.0|5|

#### 6. Menghitung nilai similaritas
```
similarities = []

target_row = df_norm.loc[row_missing, features].values.reshape(1,-1)

for i in range(len(df_norm)):

    if i == row_missing:
        similarities.append(None)
        continue

    compare_row = df_norm.loc[i, features].values.reshape(1,-1)

    dist = pairwise_distances(target_row, compare_row, metric='euclidean')[0][0]


    Si = 1 / distance_sum
    similarities.append(Si)

print("Similarity:")
similarities
```
Output:
```
Similarity:
[np.float64(0.17525799901361003),
 np.float64(0.40893918401029067),
 None,
 np.float64(0.17525799901361003)]
```
#### 7. Menghitung penyebut
Perhitungan penyebut dilakukan dengan menjumlahkan semua nilai similaritas diatas
```
denominator = sum([s for s in similarities if s is not None])

print("Nilai penyebut:")
print(denominator)
```
Output:
```
Nilai penyebut:
0.7594551820375107
```

#### 8. Menghitung pembilang
```
numerator = 0

for i in range(len(df)):
    
    if similarities[i] is None:
        continue
        
    Yjh = df_norm.loc[i, missing_column]
    numerator += similarities[i] * Yjh

print("Nilai pembilang:")
print(numerator)
```
Output:
```
Nilai pembilang:
0.40893918401029067
```

#### 9. Menghitung nilai imputasi
Hanya perlu membagi nilai pembilang dengan penyebut nya
```
imputed_value = numerator / denominator

print("Nilai imputasi:")
print(imputed_value)
```
Output:
```
Nilai imputasi:
0.5384638800056176
```
Nilai diatas adalah hasil akhir untuk mengisi missing values yang ada.


## 2. Normalisasi data 
### 1. Materi normalisasi data
Normalisasi data merupakan proses transformasi nilai pada atribut yang sudah ada dengan cara mengubah skalanya ke dalam rentang tertentu agar distribusi nilainya menjadi lebih sesuai untuk proses analisis. Tahapan ini termasuk dalam prepocessing data

Tujuan dari normalisasi adalah menyamakan skala antar variabel sehingga tidak ada atribut yang terlalu mendominasi karena memiliki nilai yang jauh lebih besar dibandingkan atribut lainnya. Dengan melakukan normalisasi, setiap fitur dalam dataset dapat memberikan kontribusi yang lebih seimbang dalam proses perhitungan dan membantu meningkatkan kemampuan model dalam menghasilkan prediksi yang lebih akurat.

### 2. Macam-macam normalisasi data

#### 1. Min-Max Normalization
Min-Max Normalization adalah metode normalisasi yang digunakan untuk mengubah nilai data ke dalam rentang tertentu, biasanya 0 sampai 1. Metode ini bekerja dengan cara mengurangi setiap nilai data dengan nilai minimum pada dataset, kemudian membaginya dengan selisih antara nilai maksimum dan minimum.
$$
x' = \frac{x - x_{min}}{x_{max} - x_{min}}
$$
Keterangan:
- $x$ : nilai data asli  
- $x_{min}$ : nilai minimum pada atribut  
- $x_{max}$ : nilai maksimum pada atribut  
- $x'$ : nilai hasil normalisasi
Contoh perhitungan min-max normalization terhadap data wine quality pada kolom "Fixed acidity"

Data sebelum di normalisasi
| Fixed acidity |
| :--: |
| 7.4 |
| 7.8 |
| 7.8 |

Diketahui:
Min = 7.4
Max = 7.8

Contoh perhitungan normalisasi min-max:
$$
x' = \frac{7.4 - 7.4}{7.8 - 7.4} = 0
$$

Hasil keseluruhan data ketika sudah di normalisasi
| Fixed acidity |
| :--: |
| 0 |
| 1 |
| 1 |

#### 2. Z-score normalization
Z-Score Normalization (atau standardization) adalah metode normalisasi yang mengubah nilai data berdasarkan rata-rata (mean) dan standar deviasi dari dataset. Tujuannya adalah agar data memiliki rata-rata 0 dan standar deviasi 1, sehingga distribusi data menjadi lebih terstandarisasi.

Rumus Z-Score Normalization

$$
z = \frac{x - \mu}{\sigma}
$$

Keterangan:
- \(x\) : nilai data asli  
- \(\mu\) : rata-rata data  
- \(\sigma\) : standar deviasi data  
- \(z\) : nilai hasil normalisasi

Contoh perhitungan Z-score normalization terhadap data wine quality pada kolom "Fixed acidity"

Data sebelum di normalisasi
| Fixed acidity |
| :--: |
| 7.4 |
| 7.8 |
| 7.8 |

Menghitung nilai mean (rata-rata):
$$
\mu = \frac{7.4 + 7.8 + 7.8}{3}
$$
$$
\mu = \frac{23}{3}
$$
$$
\mu = 7.6666667
$$

Menghitung standar deviasi:
rumus standar deviasi:
$$
\sigma = \sqrt{\frac{\sum (x - \mu)^2}{n}}
$$
Perhitungan dengan nilai rata-rata = 7.6666667dan jumlah data = 3
$$
(7.4 - 7.6666667)^2  = 0.071111
$$

$$
(7.8 - 7.6666667)^2  = 0.0177778
$$

$$
(7.8 - 7.6666667)^2  = 0.0177778
$$
Jumlahkan:
$$
\sigma =
\sqrt{
\frac{
0.071111 + 0.0177778 + 0.0177778
}{3}
}
$$
Bagi dan akarkan nilai nya:
$$
\sigma =
\sqrt{
\frac{0.1066667}{3}
}
$$
$$
\sigma = 0.1885618
$$

Diketahui:

- Mean ($\mu$) = 7.6666667
- Standar deviasi ($\sigma$) = 0.1885618

Contoh untuk perhitungan pada data pertama:
$$
z = \frac{7.4 - 7.6666667}{0.1885618}
$$

$$
z = −1.41421356
$$

Hasil normalisasi keseluran:
| Fixed acidity |
| :--: |
| -1.414214 |
| 0.70710678 |
| 0.70710678 |

#### 3. Decimal scaling normalization
Decimal Scaling adalah metode normalisasi yang dilakukan dengan menggeser titik desimal pada nilai data sehingga semua nilai berada pada rentang yang lebih kecil (biasanya kurang dari 1). Proses ini dilakukan dengan cara membagi setiap nilai data dengan $10^j$ di mana j adalah jumlah digit dari nilai absolut terbesar dalam dataset.

Rumus decimal scaling:
$$
x' = \frac{x}{10^j}
$$
Keterangan:
$x$ : nilai data asli  
$j$ : jumlah digit dari nilai absolut terbesar dalam data  
$x'$ : nilai hasil normalisasi

Contoh perhitungan Deciman scaling normalization terhadap data wine quality pada kolom "Fixed acidity"

Data sebelum di normalisasi
| Fixed acidity |
| :--: |
| 7.4 |
| 7.8 |
| 7.8 |

Tentukan nilai j:
Nilai terbesar dalam data adalah 7.8, dimana pada data 7.8 digit sebelum koma adalah 1, maka
$$
j = 1
$$
Contoh perhitungan:
$$
x'_1 = \frac{7.4}{10^1} = 0.74
$$

Sehingga hasil akhir data:
| Fixed acidity |
| :--: |
| 0.74 |
| 0.78 |
| 0.78 |

### 3. Normalisasi data menggunakan python dengan library Scikit-learn
Selain melakukan perhitungan normalisasi secara manual, normalisasi data juga dapat dilakukan menggunakan library Python yaitu scikit-learn (sklearn). Library ini menyediakan berbagai fungsi preprocessing data yang memudahkan proses normalisasi sebelum data digunakan dalam proses analisis atau machine learning.

Kita pakai contoh yang sama, yaitu 3 data mengenai "fixed acidity"
| Fixed acidity |
| :--: |
| 7.4 |
| 7.8 |
| 7.8 |

#### 1. Min-max normalization
Pada sklearn ada library MinMaxSCaler yang dapat digunaka untuk normalisasi ini

```
import pandas as pd
import numpy as np
from sklearn.preprocessing import MinMaxScaler

data = np.array([[7.4],[7.8],[7.8]])

df = pd.DataFrame(data, columns=['nilai'])

scaler = MinMaxScaler()
df['minmax_normalisasi'] = scaler.fit_transform(df[['nilai']])

print(df)
```
Pandas dan numpy hanya digunaka untuk membantu menampilkan hasil agar terlihat lebih baik, perhitungan dilakukan murni melalui fungsi Scikit-learn

Output:
| Fixed acidity | minmax_normalisasi |
| :---: | :---: |
| 7.4 | 0.0 |
| 7.8 | 1.0 |
| 7.8 | 1.0 |

#### 2. Z-score normalization
Dalam scikit-learn ini disebut StandardScaler di dalam library nya

```
import pandas as pd
import numpy as np
from sklearn.preprocessing import StandardScaler

data = [7.4, 7.8, 7.8]

df = pd.DataFrame(data, columns=['Fixed acidity'])

scaler = StandardScaler()

df['zscore_normalisasi'] = scaler.fit_transform(df[['Fixed acidity']])

print(df)
```
Output:
| Fixed acidity | zscore_normalisasi |
| :---: | :---: |
| 7.4 | -1.414214 |
| 7.8 | 0.707107 |
| 7.8 | 0.707107 |

#### 3. Decimal scaling normalization
Berbeda dengan 2 metode diatas, decimal scalig tidak secara langsung tersedia pada library scikit-learn, maka dari itu kita akan membuat fungsi alternatif nya secara mandiri
```
import numpy as np

data = np.array([7.4, 7.8, 7.8])

max_val = np.max(np.abs(data))

j = len(str(int(max_val)))

normalized = data / (10 ** j)

print("Data asli:", data)
print("Hasil normalisasi:", normalized)
```

Output:
```
Data asli: [7.4 7.8 7.8]
Hasil normalisasi: [0.74 0.78 0.78]
```