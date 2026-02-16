# Data Preparation

Tahap Data Preparation merupakan fase lanjutan dalam metodologi CRISP-DM setelah proses Data Understanding selesai dilakukan. Pada tahap ini, data yang sebelumnya telah dianalisis karakteristik dan strukturnya akan dipersiapkan agar dapat digunakan secara optimal pada tahap pemodelan (modeling).

Secara umum, data yang diperoleh dari suatu sumber sering kali masih berada dalam kondisi mentah (raw data), sehingga memerlukan proses penyesuaian, pembersihan, dan pengorganisasian terlebih dahulu. Tujuan utama dari tahap ini adalah memastikan bahwa data yang digunakan dalam proses pembangunan model memiliki kualitas yang baik, konsisten, serta sesuai dengan kebutuhan analisis.

## 1. Memilih Data

Pada tahap ini dilakukan pemilihan data yang akan digunakan dalam proses analisis dan pemodelan.
Dataset yang digunakan adalah dataset Iris, yang terdiri dari 150 data dengan 5 atribut, yaitu:

- sepal_length
- sepal_width
- petal_length
- petal_width
- species

## 2. Membersihkan Data

### - Cek Missing Value

|  |  |
| :-- | :-- |
| sepal_length | 0 |
| sepal_width | 0 |
| petal_length | 0 |
| petal_width | 0 |
| species | 0 |

Berdasarkan hasil pemeriksaan pada data understanding, seluruh atribut memiliki nilai 0 pada missing value. Hal ini menunjukkan bahwa dataset sudah lengkap dan tidak memerlukan penanganan missing value.

### Cek Duplikat Data

Berdasarkan hasil pemeriksaan pada data understanding, terdapat 3 data duplikat pada dataset. Data tersebut kemudian akan dihapus pada tahap pembersihan data untuk memastikan kualitas dataset tetap baik.

```
np.int64(3)
```

Data yang menjadi duplikat dapat kita tampilkan dengan code sebagai berikut:

```
duplikat = df[df.duplicated()]
print(duplikat)
```

Output:
| sepal_length | sepal_width | petal_length | petal_length | petal_width | species |
| :-- | :-- | :-- | :-- | :-- | :-- |
| 34 | 4.9 | 3.1 | 1.5 | 0.1 | Iris-setosa |
| 37 | 4.9 | 3.1 | 1.5 | 0.1 | Iris-setosa |
| 142 | 5.8 | 2.7 | 5.1 | 0.9 | Iris-virginica |

Berdasarkan hasil di atas, data yang menjadi dupllikat berada pada index 34,37,142.

Selanjutnya pembersihan data duplikat
```
df = df.drop_duplicates()
```
Setelah dilakukan penghapusan, jumlah data berkurang dan tidak terdapat lagi data duplikat, sehingga dataset menjadi lebih bersih dan siap digunakan untuk proses analisis dan pemodelan.

![original image](https://cdn.mathpix.com/snip/images/n9MAjgsSondmmBsZjTsThSPfzJmFd8hVKyvfmxiaNLY.original.fullsize.png)

### - Kesimpulan

Berdasarkan proses pembersihan data yang telah dilakukan, diketahui bahwa:

- Tidak terdapat missing value
- Tidak terdapat data duplikat

Dengan demikian, dataset sudah bersih dan siap digunakan untuk tahap selanjutnya, yaitu transformasi data dan pemodelan.

## 3. Integrasi Data

Tahap data integration bertujuan untuk menggabungkan data dari berbagai sumber menjadi satu dataset yang konsisten dan terstruktur.

Pada penelitian ini, dataset Iris diperoleh dari satu sumber dan seluruh atribut yang dibutuhkan sudah tersedia dalam satu file. Atribut tersebut meliputi sepal_length, sepal_width, petal_length, petal_width, dan species.

Karena seluruh data sudah terintegrasi dalam satu dataset dan tidak terdapat sumber data lain yang perlu digabungkan, maka tidak diperlukan proses integrasi tambahan.

## 4. Download hasil perubahan

Pada tahap ini kita dapat menyimpan hasil perubahan file data IRIS untuk dilakukan tahapan selanjutnya nanti yaitu modelling, kita dapat menyimpan nya melalui google drive dengan code:

```
df.to_csv("/content/drive/MyDrive/tugas/hasil_olah_iris.csv", index=False)
```
Maka pada folder tugas akan ada file hasil_olah_iris.csv