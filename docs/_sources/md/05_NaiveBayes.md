# Analisis Data Menggunakan Naive Bayes

## 1. Naive Bayes
Naive Bayes adalah metode klasifikasi berbasis probabilitas yang menggunakan konsep dari Teorema Bayes. Metode ini mengasumsikan bahwa setiap fitur bersifat independen terhadap fitur lainnya.

### Rumus Naive Bayes

$$
P(C_i \mid X) = \frac{P(X \mid C_i) \cdot P(C_i)}{P(X)}
$$

### Keterangan:

- $P(C_i \mid X)$ : probabilitas suatu kelas terhadap data  
- $P(X \mid C_i)$ : probabilitas data terhadap kelas  
- $P(C_i)$ : probabilitas kelas (prior)  
- $P(X)$ : probabilitas data keseluruhan  




## 3. Dataset

Dataset yang digunakan dalam penelitian ini adalah **Play Tennis Dataset: Weather-based Classifier** 

[Dataset](https://www.kaggle.com/datasets/milapgohil/play-tennis-dataset-weather-based-classifier)

Dataset ini mengenai keputusan akan melakukan olahraga tennis atau tidak berdasarkan fitur yang ada. data diperoleh dari Kaggle.

Dataset diatas berisi dengan tipe data kategorikal semua dengan 4 fitur dan 1 class. Dimana kolom day diabaikan untuk digunakan

**Struktur Dataset**

Dalam catatan ini digunakan beberapa fitur utama:

- Outlook
- Temperature
- Humidity
- wind  
- Play (class)  



**Penggunaan Dataset**

Dalam tugas ini, dataset digunakan dengan dua pendekatan:

1. **Pengolahan di Excel**
   - Diambil sebanyak **20 data (sampling)**
   - Digunakan untuk **perhitungan manual Naive Bayes**

2. **Pengolahan menggunakan KNIME Script Python (sklearn)**
   - Menggunakan **seluruh dataset**
   - Digunakan untuk:
     - Training model
     - Testing model
     - Evaluasi hasil klasifikasi



## 4. Proses Perhitungan

### 4.1 Perhitungan Menggunakan Excel
Terdapat  20 data yang digunakan dalam perhitungan manual ini, dimana data dibagi dengan perbandingan 80:20. Dengan data training 16 dan data testing 4.

Data training:
| Day | Outlook  | Temperature | Humidity | Wind   | Play |
|-----|----------|-------------|----------|--------|------|
| D1  | Overcast | Mild        | Normal   | Strong | Yes  |
| D2  | Sunny    | Mild        | Normal   | Strong | Yes  |
| D3  | None     | Mild        | High     | Strong | No   |
| D4  | Sunny    | Mild        | High     | Weak   | Yes  |
| D5  | Sunny    | Cool        | Normal   | Strong | Yes  |
| D6  | None     | Cool        | Normal   | Strong | Yes  |
| D7  | Sunny    | Mild        | High     | Weak   | Yes  |
| D8  | Sunny    | Mild        | Normal   | Weak   | Yes  |
| D9  | Rainy    | Mild        | Normal   | Weak   | No   |
| D10 | Rainy    | Cool        | High     | Weak   | No   |
| D11 | Rainy    | Mild        | Normal   | Weak   | No   |
| D12 | Sunny    | Mild        | Normal   | None   | Yes  |
| D13 | Rainy    | Hot         | Normal   | Weak   | No   |
| D14 | Sunny    | Cool        | Normal   | Weak   | Yes  |
| D15 | Sunny    | Mild        | High     | Weak   | Yes  |
| D16 | Overcast | Mild        | High     | Strong | Yes  |

Data test:
| Day | Outlook  | Temperature | Humidity | Wind   | Play |
|-----|----------|-------------|----------|--------|------|
| D17 | Overcast | Mild        | High     | Weak   | Yes  |
| D18 | Sunny    | Cool        | High     | Weak   | Yes  |
| D19 | None     | Mild        | High     | Weak   | No   |
| D20 | Rainy    | Hot         | High     | Strong | No   |


#### 4.1.1 Menghitung Probabilitas Prior
$$
P(C_i) = \frac{\text{jumlah data pada kelas } C_i}{\text{total data}}
$$
Berdasarkan data training diatas, kita bisa melihat prior dengan hasil sebagai berikut:
![original image](https://cdn.mathpix.com/snip/images/MbTREEMwmbSxujp2K02E8Joyj4SgzX1o8gRKoqIimwg.original.fullsize.png)


#### 4.1.2 Menghitung Probabilitas Likelihood

a. Untuk data kategorikal/biner:
$$
P(x_j \mid C_i) = \frac{\text{jumlah kemunculan } x_j \text{ pada } C_i}{\text{jumlah data pada } C_i}
$$
Selanjutnya perhitungan likelihood dilakukan berdasarkan nilai prior yang fitur miliki. Hasil akhirnya sebagai berikut:
![original image](https://cdn.mathpix.com/snip/images/uytIyzFkN_q67pZ5wkVoe1AppmaRQRLN-ZTAHNrbdAQ.original.fullsize.png)
Misal Outlook memiliki 4 kategori, maka ke 4 nya dihitung dengan nilai prior yes dan no seperti gambar diatas

Lalu kita lihat berdasarkan data testing yang digunakan, sebagai contoh data D17, maka pilih likelihood yang sesuai dengan nilai diatas untuk dikalikan semuanya. Hasil akhir akan ada di likelihood gabungan


#### 4.1.3 Menghitung Likelihood Gabungan
$$
P(X \mid C_i) = \prod_{j=1}^{n} P(x_j \mid C_i)
$$
Hasil akhir akan seperti ini:

![original image](https://cdn.mathpix.com/snip/images/r6sHgk8F8FuAcRgGra3YDH-L0TljCqHP5a9Q5I8ncJo.original.fullsize.png)



#### 4.1.4 Menghitung Posterior
Selanjutnya kita hitung posterion dengan cara mengkalikan nilai prior dengan likelihood gabungan
$$
P(X \mid C_i) \cdot P(C_i)
$$
![original image](https://cdn.mathpix.com/snip/images/Reh_NsjO2O7ZUCB4tCi7L4CERGapsr7XLGMKvXtqmXg.original.fullsize.png)


#### 4.1.5 Menentukan Hasil Klasifikasi
$$
\hat{C} = \arg\max_{C_i} \left[ P(C_i) \cdot P(X \mid C_i) \right]
$$
Dari hasil posterior pilih nilai tertinggi yang digunakan

Jika:
- $P(X|Yes) \cdot P(Yes) > P(X|No) \cdot P(No)$  
maka hasil = **Yes**



### 4.2 Perhitungan Menggunakan KNIME Script Python

Pada tahap ini kita gunakan knime dengan menggunakan seluruh dataset yang ada

Langkah-langkah:
1. Baca data dari csv
2. Cek dan tangani missing values
3. Masukkan python script pada node script python

#### 4.2.1 Node csv reader
![original image](https://cdn.mathpix.com/snip/images/MbrhanYpFUG_G9s0or9cjZW6Rhw8_O-P3twzBLjU1cM.original.fullsize.png)
Gunakan node ini untuk membaca data dari csv yang ada. Hasilnya:
![original image](https://cdn.mathpix.com/snip/images/OCMAKvGpz77v0TtIsnuhmko8I_V9zsIb3aibL1Xadmk.original.fullsize.png)

#### 4.2.1 Missing values
![original image](https://cdn.mathpix.com/snip/images/ZEDbXstQJvUyrOM4Lewgt89u9e2WSF7IJExsU6i-JTE.original.fullsize.png)

Digunakan remove row jika terdapat missing value di dalamnya,namun dapat dilihat jumlah data tetap konsisten artinya data tidak terdapat missing value di dalamnya.

#### 4.2.1 Node python script
Code dalam node script python
```
import knime.scripting.io as knio
import pandas as pd
from sklearn.model_selection import train_test_split
from sklearn.preprocessing import OrdinalEncoder
from sklearn.naive_bayes import CategoricalNB
from sklearn.metrics import accuracy_score, classification_report

# 1. Ambil input table dari KNIME (ganti read_csv)
df = knio.input_tables[0].to_pandas()

# 2. Drop kolom yang tidak diperlukan
df = df.drop("Day", axis=1)

# 3. Pisahkan fitur (X) dan target (y)
X = df.drop("Play", axis=1)
y = df["Play"]

# 4. Encode data kategorikal
encoder = OrdinalEncoder()
X_encoded = encoder.fit_transform(X)

# 5. Split data 80:20
X_train, X_test, y_train, y_test = train_test_split(
    X_encoded, y, test_size=0.2, random_state=42
)

# 6. Model Naive Bayes Categorical
model = CategoricalNB()

# 7. Training
model.fit(X_train, y_train)

# 8. Prediksi
y_pred = model.predict(X_test)

# 9. Evaluasi
accuracy = accuracy_score(y_test, y_pred)
report = classification_report(y_test, y_pred)
print("Accuracy:", accuracy)
print("\nClassification Report:\n", report)

# 10. Output hasil prediksi ke port KNIME (wajib ada!)
result_df = pd.DataFrame({
    "Actual": y_test.values,
    "Predicted": y_pred
})
knio.output_tables[0] = knio.Table.from_pandas(result_df)
```

Dalam script tersebut digunakan modul sklearn categoricalNB karena seluruh data di dalam dataset berisi kategorikal

Alur script python:
- Membaca node csv knime
- Drop kolom yang tidak digunakan yaitu kolom 'Day'
- Pisahkan fitur dengan label/class
- Encode data kategorikal agar menjadi numerik tanpa memiliki tingkatan
- Split data dengan format 80:20 training dan testing
- Masukkan ke dalam model sklearn naive bayes categoricalNB
- Lakukan training
- Prediksi data testing
- lalu lakukan evaluasi dengan melihat hasil accuracy score

Hasil akhir program dalam node knime akan menunjukan data actual dan data prediksi versi naive bayes seperti dibawah:

![original image](https://cdn.mathpix.com/snip/images/q3VfFy4fE9q9JPR9x1pM9DRL0R9oX5qNvaM732PMLVg.original.fullsize.png)
Lalu hasil accuracy seperti dibawah ini:

![original image](https://cdn.mathpix.com/snip/images/OGb6d5XaowuCya-SAs6JIdnB--nS0bRSfA0SKd8QxkM.original.fullsize.png)

## Kesimpulan

Metode Naive Bayes dapat digunakan untuk melakukan klasifikasi pada dataset Playing tennis dengan baik, baik secara manual maupun menggunakan tools seperti KNIME dan Python.