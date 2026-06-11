# Model Explainability dengan Skforecast
Link mengakses website refrensi: [SKforecast](https://skforecast.org/0.15.1/user_guides/explainability.html#permutation-feature-importance)

Link mengakses notebook colab yang dibuat: [Notebook](https://colab.research.google.com/drive/1o1pshJwLPAUynOabkmg-23f15_228mFw?usp=sharing)


---

## 1. Analisis Prediksi — Tentang Apa?

Website diatas ini membangun model untuk memprediksi **total permintaan listrik harian** (Demand) di negara bagian Victoria, Australia. Prediksi dilakukan berdasarkan dua sumber informasi utama: pola permintaan listrik di hari-hari sebelumnya, dan suhu udara harian (Temperature) sebagai faktor eksternal.

Tujuan utama notebook ini bukan sekadar menghasilkan prediksi, melainkan **menjelaskan mengapa model membuat prediksi tertentu** — proses yang dikenal sebagai *model explainability*. Dengan kata lain, setelah model dilatih dan menghasilkan angka prediksi, dilakukan analisis lebih lanjut untuk menjawab pertanyaan seperti: fitur mana yang paling berpengaruh? Apakah suhu yang tinggi cenderung meningkatkan atau menurunkan prediksi permintaan listrik? Faktor apa yang mendorong prediksi pada hari tertentu?

Ada 2 tiga metode explainability digunakan secara bersamaan untuk menjawab hal tersebut: **SHAP values**, **permutation feature importance**, dan **partial dependence plots**.

---

## 2. Bentuk Data Training — Input dan Output

Sebelum model dilatih, skforecast mengubah data time series mentah menjadi format angka yang bisa dipahami oleh estimator berbasis machine learning. Proses ini dilakukan oleh metode create_train_X_y().

**Input (X_train)** terdiri dari:

| Kolom | Deskripsi |
|---|---|
| lag_1 | Nilai Demand 1 hari sebelumnya |
| lag_2 | Nilai Demand 2 hari sebelumnya |
| lag_3 | Nilai Demand 3 hari sebelumnya |
| lag_4 | Nilai Demand 4 hari sebelumnya |
| lag_5 | Nilai Demand 5 hari sebelumnya |
| lag_6 | Nilai Demand 6 hari sebelumnya |
| lag_7 | Nilai Demand 7 hari sebelumnya |
| Temperature | Suhu rata-rata harian (variabel) |

**Output (y_train)** adalah:

| Kolom | Deskripsi |
|---|---|
| y | Nilai Demand pada hari yang diprediksi |


---

## C. Apa Itu Lag?

Lag adalah teknik untuk mengubah data time series menjadi fitur yang bisa digunakan oleh model machine learning. Secara sederhana, lag ke-*n* dari suatu kolom berarti **nilai kolom tersebut dari *n* langkah waktu sebelumnya**.

Sebagai contoh, jika data harian dan konfigurasi lags=7:

| Tanggal | Demand (aktual) | lag_1 | lag_2 | lag_3 | ... | lag_7 |
|---|---|---|---|---|---|---|
| 7 Jan 2012 | 200.693 | 205.338 | 211.066 | 213.792 | ... | 82.531 |
| 8 Jan 2012 | 200.061 | 200.693 | 205.338 | 211.066 | ... | 227.778 |

Nilai lag_1 pada tanggal 7 Januari adalah nilai Demand tanggal 6 Januari, lag_2 adalah tanggal 5 Januari, dan seterusnya hingga lag_7 yang merupakan nilai 7 hari sebelumnya.

Ide di balik penggunaan lag adalah bahwa **permintaan listrik hari ini sangat dipengaruhi oleh permintaan listrik hari-hari sebelumnya** 

---
## Implementasi Reproduksi notebook
### Cell 1 — Instalasi Library

```python
!pip install shap lightgbm skforecast
```

Menginstal tiga library utama yang dibutuhkan:
- shap — library untuk menghitung dan memvisualisasikan SHAP values sebagai metode explainability.
- lightgbm — framework gradient boosting berbasis pohon yang digunakan sebagai model regresi internal.
- skforecast — library forecasting time series yang memungkinkan penggunaan estimator scikit-learn untuk prediksi multi-step.
- Sisanya sudah tersedia di dalam environment Colab

---

### Cell 2 — Inisialisasi SHAP JavaScript

```python
from IPython.display import display, HTML
shap.initjs()
```

shap.initjs() memuat library JavaScript yang diperlukan oleh SHAP untuk merender visualisasi interaktif (seperti force plot) di dalam notebook Colab.

---

### Cell 3 — Import Library

```python
import pandas as pd
import matplotlib.pyplot as plt
import shap
from sklearn.inspection import permutation_importance
from sklearn.inspection import PartialDependenceDisplay
from lightgbm import LGBMRegressor
from skforecast.datasets import fetch_dataset
from skforecast.recursive import ForecasterRecursive
```

Mengimpor seluruh library yang digunakan sepanjang notebook:
- pandas dan matplotlib untuk manipulasi data dan visualisasi.
- shap untuk analisis SHAP values.
- permutation_importance dan PartialDependenceDisplay dari sklearn.inspection untuk dua metode explainability tambahan (permutation importance dan partial dependence plot).
- LGBMRegressor sebagai model estimator.
- fetch_dataset untuk mengunduh dataset contoh dari repositori skforecast.
- ForecasterRecursive sebagai kelas forecaster utama yang digunakan.

---

### Cell 4 — Unduh Dataset

```python
data = fetch_dataset(name="vic_electricity")
data.head(3)
```

Mengunduh dataset vic_electricity yang berisi data permintaan listrik harian di Victoria, Australia. Dataset ini memiliki kolom utama Demand (permintaan listrik dalam MW) dan Temperature (suhu rata-rata di Melbourne). data.head(3) digunakan untuk melihat tiga baris pertama dataset sebagai verifikasi awal.

---

### Cell 5 — Agregasi ke Frekuensi Harian

```python
data = data.resample('D').agg({'Demand': 'sum', 'Temperature': 'mean'})
data.head(3)
```

Dataset asli memiliki frekuensi setengah jam (30 menit). Cell ini melakukan resampling ke frekuensi harian ('D') dengan cara:
- Demand dijumlahkan (sum) untuk mendapatkan total permintaan listrik per hari.
- Temperature dirata-rata (mean) untuk mendapatkan suhu harian.

Hasil agregasi diverifikasi kembali dengan head(3).

---

### Cell 6 — Split Data Train dan Test

```python
data_train = data.loc[: '2014-12-21']
data_test  = data.loc['2014-12-22':]
```

Data dibagi menjadi dua bagian berdasarkan tanggal:
- data_train — data dari awal hingga 21 Desember 2014, digunakan untuk melatih model.
- data_test — data mulai 22 Desember 2014, digunakan untuk evaluasi dan prediksi.

Pembagian berbasis tanggal ini adalah pendekatan standar untuk time series agar tidak terjadi data leakage dari masa depan ke proses pelatihan.

---

### Cell 7 — Membuat dan Melatih Forecaster

```python
forecaster = ForecasterRecursive(
                 estimator = LGBMRegressor(random_state=123, verbose=-1),
                 lags      = 7
             )

forecaster.fit(
    y    = data_train['Demand'],
    exog = data_train['Temperature']
)
```

Membuat model forecasting rekursif multi-step menggunakan ForecasterRecursive:
- estimator — model regresi internal yang digunakan adalah LGBMRegressor. Parameter random_state=123 memastikan hasil yang reproducible
- lags=7 — model menggunakan 7 nilai terakhir dari deret waktu (lag_1 hingga lag_7) sebagai fitur prediktor, yang merepresentasikan data satu minggu ke belakang.

forecaster.fit() melatih model menggunakan kolom Demand sebagai target dan Temperature

---

### Cell 8 — Feature Importances dari Model

```python
forecaster.get_feature_importances()
```

Mengambil nilai feature importance dari estimator internal menggunakan metode get_feature_importances(). Metode ini mengakses atribut feature_importances_ atau coef_ (untuk model linear) dari estimator. Hasilnya menunjukkan seberapa besar kontribusi masing-masing fitur (lag dan variabel eksogen) terhadap prediksi model.

---

### Cell 9 — Membuat Matriks Training

```python
X_train, y_train = forecaster.create_train_X_y(
                       y    = data_train['Demand'],
                       exog = data_train['Temperature']
                   )

display(X_train.head(3))
display(y_train.head(3))
```

create_train_X_y() mengekstrak matriks input (X_train) dan target (y_train) yang digunakan oleh estimator internal saat proses pelatihan. X_train berisi kolom lag_1 hingga lag_7 dan Temperature, sedangkan y_train berisi nilai Demand yang ingin diprediksi. Matriks ini dibutuhkan secara eksplisit untuk proses perhitungan SHAP values di langkah selanjutnya.

---

### Cell 10 — Membuat SHAP Explainer dan Menghitung SHAP Values

```python
explainer = shap.TreeExplainer(forecaster.estimator)
shap_values = explainer.shap_values(X_train)
```

Membuat SHAP explainer dan menghitung SHAP values dari data training:
- shap.TreeExplainer digunakan karena estimator yang dipakai adalah model berbasis pohon (LGBMRegressor). Setiap jenis model memiliki explainer yang sesuai di library SHAP.
- forecaster.estimator mengakses model regresi internal dari forecaster.
- explainer.shap_values(X_train) menghitung SHAP value untuk setiap observasi dan setiap fitur di X_train. Nilai ini merepresentasikan kontribusi masing-masing fitur terhadap prediksi individual.

---

### Cell 11 — SHAP Summary Plot (Bar)

```python
shap.summary_plot(shap_values, X_train, plot_type="bar")
```

Menampilkan summary plot dalam bentuk bar chart. Setiap bar merepresentasikan rata-rata absolut SHAP value dari masing-masing fitur di seluruh data training. Plot ini memberikan gambaran global tentang fitur mana yang paling berpengaruh terhadap prediksi model secara keseluruhan.

---

### Cell 12 — SHAP Summary Plot (Beeswarm)

```python
shap.summary_plot(shap_values, X_train)
```

Menampilkan summary plot dalam bentuk beeswarm (default). Berbeda dengan bar chart, plot ini menunjukkan distribusi SHAP values untuk setiap fitur di seluruh observasi. Warna titik mencerminkan nilai fitur (merah = tinggi, biru = rendah), sehingga bisa terlihat bagaimana arah dan besaran pengaruh nilai fitur terhadap prediksi.

---

### Cell 13 — Force Plot: Satu Observasi dari Training Set

```python
plot = shap.force_plot(explainer.expected_value, shap_values[0, :], X_train.iloc[0, :])
html_content = f"""
<html>
<head>{shap.getjs()}</head>
<body>{plot.html()}</body>
</html>
"""
display(HTML(html_content))
```

Menampilkan force plot untuk observasi pertama (index 0) dari data training. Force plot adalah visualisasi lokal yang menunjukkan bagaimana masing-masing fitur mendorong prediksi ke atas atau ke bawah dari nilai baseline (explainer.expected_value).

Karena shap.force_plot memerlukan JavaScript untuk dirender, visualisasi dibungkus secara manual menggunakan shap.getjs() dan display(HTML(...)) agar dapat tampil dengan benar di lingkungan Colab yang tidak memuat JS SHAP secara otomatis.

---

### Cell 14 — Force Plot: Banyak Observasi dari Training Set

```python
plot = shap.force_plot(explainer.expected_value, shap_values[:200, :], X_train.iloc[:200, :])
html_content = f"""
<html>
<head>{shap.getjs()}</head>
<body>{plot.html()}</body>
</html>
"""
display(HTML(html_content))
```

Serupa dengan cell sebelumnya, namun force plot diterapkan pada 200 observasi pertama sekaligus. Hasilnya adalah visualisasi interaktif yang memungkinkan eksplorasi pola kontribusi fitur di banyak prediksi dalam satu tampilan, sering disebut sebagai "stacked force plot".

---

### Cell 15 — SHAP Dependence Plot

```python
fig, ax = plt.subplots(figsize=(7, 4))
shap.dependence_plot("Temperature", shap_values, X_train, ax=ax)
```

Menampilkan dependence plot untuk fitur Temperature. Plot ini menunjukkan hubungan antara nilai fitur Temperature dengan SHAP value-nya — yaitu seberapa besar dan ke arah mana suhu memengaruhi prediksi permintaan listrik. SHAP secara otomatis memilih fitur lain untuk pewarnaan berdasarkan interaksi terkuat yang ditemukan.

---

### Cell 16 — Prediksi

```python
predictions = forecaster.predict(steps=10, exog=data_test['Temperature'])
predictions
```

Menghasilkan prediksi 10 langkah ke depan (steps=10) menggunakan variabel eksogen suhu dari data test. Prediksi dimulai dari tanggal 22 Desember 2014, mengikuti batas split data yang ditentukan sebelumnya.

---

### Cell 17 — Membuat Matriks Input untuk Prediksi

```python
X_predict = forecaster.create_predict_X(steps=10, exog=data_test['Temperature'])
X_predict
```

create_predict_X() menghasilkan matriks input yang digunakan oleh forecaster saat melakukan prediksi — bukan dari data training, melainkan dari data yang akan diprediksi. Matriks ini dibutuhkan untuk menghitung SHAP values pada hasil prediksi, bukan pada data historis.

---

### Cell 18 — Force Plot untuk Hasil Prediksi

```python
predicted_date = '2014-12-22'
iloc_predicted_date = X_predict.index.get_loc(predicted_date)
shap_values = explainer.shap_values(X_predict)

plot = shap.force_plot(
    explainer.expected_value,
    shap_values[iloc_predicted_date, :],
    X_predict.iloc[iloc_predicted_date, :]
)

html_content = f"""
<html>
<head>{shap.getjs()}</head>
<body>{plot.html()}</body>
</html>
"""
display(HTML(html_content))
```

Menjelaskan hasil prediksi untuk tanggal tertentu (22 Desember 2014) menggunakan force plot:
- X_predict.index.get_loc(predicted_date) — mencari posisi indeks integer dari tanggal yang dituju.
- explainer.shap_values(X_predict) — menghitung SHAP values berdasarkan matriks prediksi, bukan matriks training.
- Force plot kemudian ditampilkan khusus untuk baris prediksi tanggal tersebut, sehingga bisa dilihat fitur apa yang paling memengaruhi prediksi pada hari itu.

---

### Cell 21 — Permutation Feature Importance

```python
X_train, y_train = forecaster.create_train_X_y(
                       y    = data_train['Demand'],
                       exog = data_train['Temperature']
                   )

r = permutation_importance(
        estimator    = forecaster.estimator,
        X            = X_train,
        y            = y_train,
        n_repeats    = 3,
        max_samples  = 0.5,
        random_state = 123
    )

importances = pd.DataFrame({
                  'feature': X_train.columns,
                  'mean_importance': r.importances_mean,
                  'std_importance': r.importances_std
              }).sort_values('mean_importance', ascending=False)
importances
```

Menghitung permutation feature importance sebagai metode explainability alternatif dari SHAP. Cara kerjanya adalah dengan mengacak nilai satu fitur secara berulang, lalu mengukur seberapa besar performa model menurun — semakin besar penurunannya, semakin penting fitur tersebut.

Parameter yang digunakan:
- n_repeats=3 — fitur diacak sebanyak 3 kali untuk mendapatkan estimasi yang stabil.
- max_samples=0.5 — hanya menggunakan 50% data untuk mempercepat proses.
- random_state=123 — memastikan hasil yang reproducible.

Hasil disusun dalam DataFrame yang diurutkan berdasarkan rata-rata importance dari yang tertinggi.

---

### Cell 22 — Partial Dependence Plot

```python
fig, ax = plt.subplots(figsize=(9, 4))
ax.set_title("Decision Tree")
pd.plots = PartialDependenceDisplay.from_estimator(
    estimator = forecaster.estimator,
    X         = X_train,
    features  = ["Temperature", "lag_1"],
    kind      = 'both',
    ax        = ax,
)
ax.set_title("Partial Dependence Plot")
fig.tight_layout()
```

Menampilkan Partial Dependence Plot (PDP) untuk fitur Temperature dan lag_1. PDP menunjukkan efek rata-rata suatu fitur terhadap hasil prediksi, dengan menganggap fitur lain konstan (dimarginalisasi).