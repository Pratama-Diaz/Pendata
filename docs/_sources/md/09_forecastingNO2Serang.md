# Peramalan Kadar NO₂ di Daerah Serang Menggunakan K-Nearest Neighbors (KNN) Regression

## 1. Dataset

Data yang digunakan dalam penelitian ini adalah **data kadar NO₂ harian wilayah Serang, Banten**, yang diperoleh dari citra satelit Sentinel-5P melalui platform [Copernicus Data Space](https://dataspace.copernicus.eu/).

Untuk memperoleh data ini, terlebih dahulu perlu membuat akun di platform Copernicus. Dokumentasi lengkap cara pengambilan data dapat diakses di [halaman resmi Copernicus](https://documentation.dataspace.copernicus.eu/notebook-samples/openeo/NO2Covid.html), dan proses penulisan kode dilakukan melalui [JupyterLab Copernicus](https://dataspace.copernicus.eu/analyse/jupyterlab).

**Wilayah Pengambilan Data**

| Batas Wilayah | Koordinat        |
|---------------|-----------------|
| West          | 105.9735534      |
| East          | 106.3783707      |
| North         | -6.0423048       |
| South         | -6.3536129       |

**Struktur Data**

Data yang berhasil diunduh berbentuk file NetCDF (`.nc`) dengan variabel utama sebagai berikut:

| Variabel | Keterangan                  |
|----------|-----------------------------|
| `t`      | Dimensi waktu               |
| `x`      | Koordinat longitude (grid)  |
| `y`      | Koordinat latitude (grid)   |
| `crs`    | Sistem referensi proyeksi   |
| `NO2`    | Nilai kadar NO₂             |

Nilai NO₂ memiliki struktur tiga dimensi `[waktu × 9 baris × 8 kolom]`, dengan total 725 record waktu dan nilai dalam satuan mol/m².

**Penggunaan Data**

Dalam tugas ini, data diproses menggunakan **Python (scikit-learn)** dengan dua skenario eksperimen:

1. **Skenario 1** – Menggunakan 5 hari sebelumnya sebagai fitur (lag = 5)
2. **Skenario 2** – Menggunakan 10 hari sebelumnya sebagai fitur (lag = 10)

---

## 2. Proses Pengolahan Data

### 2.1 Pengumpulan Data via OpenEO

Pengambilan data dilakukan menggunakan library `openeo` yang terhubung langsung ke server Copernicus. Autentikasi dilakukan melalui mekanisme OIDC (*OpenID Connect*).

```python
!pip install openeo
import openeo

connection = openeo.connect("openeo.dataspace.copernicus.eu").authenticate_oidc()
```

Setelah autentikasi berhasil, terminal akan menampilkan output seperti berikut:

```
Visit https://... to authenticate.
✅ Authorized successfully
Authenticated using device code flow.
```

Selanjutnya, data diambil berdasarkan area dan periode yang telah ditentukan:

```python
aoi = {
    "type": "Polygon",
    "coordinates": [[
        [106.081571,  -6.0423048],
        [106.3783707, -6.0548309],
        [106.3113415, -6.3536129],
        [105.9735534, -6.3119506],
        [106.081571,  -6.0423048],
    ]]
}

s5post = connection.load_collection(
    "SENTINEL_5P_L2",
    temporal_extent=["2025-05-01", "2026-06-01"],
    spatial_extent={
        "west": 105.9735534,
        "south": -6.3536129,
        "east": 106.3783707,
        "north": -6.0423048
    },
    bands=["NO2"],
)

# Agregasi per hari untuk menghindari duplikasi data dalam satu hari
s5p_no2_daily = s5post.aggregate_temporal_period(reducer="mean", period="day")

# Agregasi spasial untuk mendapatkan satu nilai rata-rata per hari
s5p_no2_aoi = s5p_no2_daily.aggregate_spatial(reducer="mean", geometries=aoi)
```

Gunakan website [Geojson.io](https://geojson.io/) untuk mendapatkan nilai longitude dan latitude daerah yang ingin digunakan. Pada kasus ini daerah nya berada di serang, banten

Proses pengunduhan data kemudian dijalankan dengan perintah berikut:

```python
job = s5post.execute_batch(title="NO2 in Serang", outputfile="NO2Serang.nc")
```

Proses ini memerlukan waktu beberapa menit. Status pekerjaan dapat dipantau secara langsung di terminal hingga menampilkan `finished (progress 100%)`.
atau dapat dipantau melalui website [openeo](https://editor.openeo.org/) dimana kita hanya perlu login dengan credential/device yang sama dengan copernicus yang digunakan

---

### 2.2 Pra-pemrosesan Data

#### 2.2.1 Membaca File NetCDF

File `.nc` yang telah diunduh dibaca menggunakan library `netCDF4`. Pada tahap ini dilakukan juga konversi format waktu dari tipe numerik ke format tanggal yang dapat dibaca.

```python
!pip install netCDF4
import netCDF4

file_path = "NO2Serang.nc"
ds = netCDF4.Dataset(file_path)

# Lihat seluruh variabel yang tersedia
print("📦 Variabel dalam file:")
print(ds.variables.keys())
# dict_keys(['t', 'x', 'y', 'crs', 'NO2'])

# Ambil NO2
no2 = ds.variables["NO2"][:]

# Ambil Time
time = ds.variables["t"][:]

# Konversi waktu ke format tanggal
try:
    time_units = ds.variables["t"].units
    dates = netCDF4.num2date(time, units=time_units)
except Exception:
    dates = time  # fallback jika tidak ada atribut units

# Tampilkan struktur data NO2
print(type(no2))    # <class 'numpy.ma.core.MaskedArray'>
print(len(no2))     # 725 (jumlah record waktu)
print(len(no2[0]))  # 9 (baris grid)
print(len(no2[0][0])) # 8 (kolom grid)
```

Output:

```
📦 Variabel dalam file:
dict_keys(['t', 'x', 'y', 'crs', 'NO2'])
<class 'numpy.ma.MaskedArray'>
395
9
8
0.00012387814
```

Untuk memahami bentuk data, dapat dilihat 10 record pertama:

```python
print("Contoh data pertama:")
for i in range(0, 10):
    print(no2[i])
```

Output:

```
Contoh data pertama:
[[0.0001238781405845657 -- -- 4.7935303882695735e-05
  5.105010131956078e-05 6.0547299653990194e-05 4.1275794501416385e-05
  5.8968736993847415e-05]
 [-- -- -- 4.7935303882695735e-05 5.974736632197164e-05
  7.798029400873929e-05 6.312130426522344e-05 4.356853605713695e-05]
 [-- -- -- -- 5.974736632197164e-05 7.798029400873929e-05 --
  6.484214100055397e-05]
 [-- -- -- -- 5.0653310609050095e-05 0.00011710789112839848 --
  6.484214100055397e-05]
 [-- -- -- -- 4.177606024313718e-05 3.708268195623532e-05 -- --]
 [-- -- -- -- 4.177606024313718e-05 3.708268195623532e-05
  3.902178650605492e-05 --]
 [-- -- -- -- 4.3274485506117344e-05 2.5859582819975913e-05
  3.902178650605492e-05 --]
 [-- -- -- -- 7.63036441639997e-05 6.152599962661043e-05]]
```

Dari output tersebut terlihat bahwa data mengandung nilai `--` yang merupakan *masked value* — kondisi umum pada data satelit akibat tutupan awan atau keterbatasan cakupan orbit.

#### 2.2.2 Menangani Missing Value pada Data Spasial

Missing value pada data NO₂ ditangani menggunakan metode **Interpolasi Linear** secara per-titik grid. 

```python
import numpy as np
import pandas as pd

# Ganti nilai masked dengan NaN agar dapat diinterpolasi
no2_filled = no2.filled(np.nan)

# Loop tiap titik grid (y, x)
for i in range(no2.shape[1]):     # 9 baris
    for j in range(no2.shape[2]): # 8 kolom
        series = pd.Series(no2[:, i, j])
        no2_filled[:, i, j] = series.interpolate(
            method='linear', limit_direction='both'
        ).to_numpy()
```

Dengan interpolasi linear, nilai yang hilang diisi berdasarkan nilai di sekitarnya (baik dari sisi waktu sebelum maupun sesudah)

#### 2.2.3 Agregasi Spasial dan Konversi Waktu

Setelah missing value ditangani, seluruh nilai NO₂ dalam satu hari dirata-ratakan menjadi satu nilai tunggal. Format waktu juga disederhanakan dari `datetime` penuh menjadi format tanggal `YYYY-MM-DD`.

```python
new_dates = []
new_no2 = []

for i in range(len(dates)):
    new_date = dates[i].strftime('%Y-%m-%d')
    new_dates.append(new_date)
    new_no2.append(np.mean(no2_filled[i]))

df = pd.DataFrame({
    "date": new_dates,
    "NO2": new_no2
})

# Simpan ke CSV
df.to_csv("NO2_Serang_timeseries.csv", index=False)
```

Contoh bentuk data yang dihasilkan:

```
         date       NO2
0  2025-05-01  0.000027
1  2025-05-02  0.000024
2  2025-05-03  0.000024
...
```

#### 2.2.4 Pengecekan Kelengkapan Data Harian

Tidak semua tanggal dalam rentang pengamatan dijamin tersedia dalam data satelit. Pengecekan dilakukan untuk memastikan seluruh hari dalam periode 1 Mei 2025 – 1 Juni 2026 hadir dalam dataset:

```python
import pandas as pd
import numpy as np

df = pd.read_csv("NO2_Serang_timeseries.csv")
df['date'] = pd.to_datetime(df['date'])

start_date = "2025-05-01"
end_date   = "2026-06-01"
full_range = pd.date_range(start=start_date, end=end_date, freq='D')

missing_dates = full_range.difference(df['date'])

print(f"Jumlah hari missing: {len(missing_dates)}")
print("Daftar tanggal missing:")
print(missing_dates)
```

Output:

```
Jumlah hari missing: 2
Daftar tanggal missing:
DatetimeIndex(['2025-10-06', '2026-06-01'], dtype='datetime64[ns]', freq=None)
```

Apabila ditemukan tanggal yang hilang, data dilengkapi kembali menggunakan interpolasi linear berbasis indeks waktu:

```python
df = df.sort_values('date')
df = df.set_index('date').reindex(full_range)
df.index.name = 'date'

# Interpolasi linear berdasarkan indeks waktu
df['NO2'] = df['NO2'].interpolate(method='time')

# Tangani NaN yang mungkin tersisa di ujung data
df['NO2'] = df['NO2'].bfill().ffill()

df.to_csv("no2_timeseries_interpolated.csv")
```

Setelah proses ini, dilakukan verifikasi ulang untuk memastikan tidak ada tanggal yang masih hilang:

```
Jumlah hari missing: 0
Daftar tanggal missing:
DatetimeIndex([], dtype='datetime64[ns]', freq='D')
```

#### 2.2.5 Deteksi dan Penanganan Outlier Metode IQR

Outlier dalam data time series dapat membiaskan model secara signifikan. Deteksi outlier dilakukan menggunakan metode **Interquartile Range (IQR)** dengan formula:

$$
\text{Batas Bawah} = Q_1 - 1.5 \times IQR
$$

$$
\text{Batas Atas} = Q_3 + 1.5 \times IQR
$$

$$
IQR = Q_3 - Q_1
$$

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt

df = pd.read_csv("no2_timeseries_interpolated.csv")
df['date'] = pd.to_datetime(df['date'])

# Hitung IQR
Q1 = df['NO2'].quantile(0.25)
Q3 = df['NO2'].quantile(0.75)
IQR = Q3 - Q1

lower_bound = Q1 - 1.5 * IQR
upper_bound = Q3 + 1.5 * IQR

# Identifikasi outlier
outliers_iqr = df[(df['NO2'] < lower_bound) | (df['NO2'] > upper_bound)]

print("Jumlah Outlier (IQR):", len(outliers_iqr))
print(outliers_iqr[['date', 'NO2']].head())
```

Visualisasi dilakukan untuk mempermudah interpretasi posisi outlier terhadap keseluruhan deret waktu:

```python
plt.figure(figsize=(15, 5))
plt.plot(df['date'], df['NO2'], label="NO2", linewidth=1)

plt.scatter(outliers_iqr['date'], outliers_iqr['NO2'],
            color='red', marker='o', label="Outliers")

plt.axhline(upper_bound, color='orange', linestyle='dashed', label="Upper Bound (IQR)")
plt.axhline(lower_bound, color='blue',   linestyle='dashed', label="Lower Bound (IQR)")

plt.title("Deteksi Outlier Data NO2 (Metode IQR)")
plt.xlabel("Tanggal")
plt.ylabel("Kadar NO2")
plt.legend()
plt.tight_layout()
plt.xticks(
    ticks=[df['date'].iloc[0], df['date'].iloc[-1]],
    labels=[df['date'].iloc[0].strftime('%Y-%m-%d'),
            df['date'].iloc[-1].strftime('%Y-%m-%d')]
)
plt.show()
```
![Visualisasi Outlier](img/outlier.png)

Karena data bersifat time series, data outlier tidak dihapus begitu saja melainkan digantikan dengan nilai interpolasi linear agar urutan temporal tetap terjaga:

```python
# Tandai outlier sebagai NaN
df['NO2_cleaned'] = df['NO2'].mask(
    (df['NO2'] < lower_bound) | (df['NO2'] > upper_bound)
)

print("Jumlah nilai outlier:", df['NO2_cleaned'].isna().sum())

# Isi kembali dengan interpolasi linear
df['NO2_filled'] = df['NO2_cleaned'].interpolate(method='linear')
df['NO2_filled'] = df['NO2_filled'].bfill().ffill()

print("Jumlah missing setelah interpolasi:", df['NO2_filled'].isna().sum())
```

Visualisasi data setelah outlier ditangani:

```python
plt.figure(figsize=(15, 5))
plt.plot(df['date'], df['NO2_filled'], label="NO2 (Interpolated)", linewidth=1)
plt.xticks(
    ticks=[df['date'].iloc[0], df['date'].iloc[-1]],
    labels=[df['date'].iloc[0].strftime('%Y-%m-%d'),
            df['date'].iloc[-1].strftime('%Y-%m-%d')]
)
plt.title("Plot Data NO2 Setelah Outlier Removal & Interpolasi")
plt.xlabel("Tanggal")
plt.ylabel("Kadar NO2")
plt.legend()
plt.tight_layout()
plt.show()
```

![Visualisasi Outlier](img/outlierDone.png)
---

## 3. Pemodelan Menggunakan KNN Regression

### 3.1 Uji Korelasi Lag

Data time series bersifat *unsupervised* hanya ada satu kolom nilai tanpa label. Untuk dapat menggunakan algoritma supervised seperti KNN, data perlu diubah terlebih dahulu menjadi format *supervised* dengan memanfaatkan nilai-nilai sebelumnya sebagai fitur.

Fungsi berikut mengubah deret waktu menjadi format supervised dengan fitur lag dari `t-n` hingga `t-1`, dan label `t` sebagai target prediksi:

```python
import pandas as pd

def create_supervised(data, n_lag=4):
    df_supervised = pd.DataFrame()

    for i in range(n_lag, 0, -1):
        df_supervised[f'NO2(t-{i})'] = data.shift(i)

    df_supervised['NO2(t)'] = data
    df_supervised.dropna(inplace=True)

    return df_supervised
```

Uji korelasi dilakukan menggunakan 15 lag untuk menentukan fitur mana yang paling berpengaruh:

```python
supervised_df30 = create_supervised(df['NO2_filled'], n_lag=15)

lag_cols    = supervised_df30.drop(columns="NO2(t)").columns
correlations = supervised_df30[lag_cols].corrwith(supervised_df30['NO2(t)'])

print(correlations)
```

```
NO2(t-15)    0.348152
NO2(t-14)    0.371540
NO2(t-13)    0.399174
NO2(t-12)    0.431341
NO2(t-11)    0.463730
NO2(t-10)    0.483874
NO2(t-9)     0.500684
NO2(t-8)     0.527356
NO2(t-7)     0.559143
NO2(t-6)     0.596793
NO2(t-5)     0.648854
NO2(t-4)     0.698740
NO2(t-3)     0.767762
NO2(t-2)     0.850199
NO2(t-1)     0.927461
dtype: float64

```

Hasil uji korelasi menunjukkan bahwa semakin pendek jarak lag, semakin tinggi nilai korelasinya terhadap nilai target. Fitur dengan lag t-1 hingga t-5 umumnya memiliki korelasi di atas 0.5, sehingga digunakan sebagai dasar pemilihan skenario eksperimen.

### 3.2 Pembentukan Dataset Supervised

Berdasarkan hasil analisis korelasi, dua skenario eksperimen dibentuk:

**Skenario 1 — 5 Hari Sebelumnya (lag = 5)**

```python
supervised_df5 = create_supervised(df['NO2_filled'], n_lag=5)

print(supervised_df5)
print(supervised_df5.shape)
```

Contoh output:

```
     NO2(t-5)  NO2(t-4)  NO2(t-3)  NO2(t-2)  NO2(t-1)    NO2(t)
5    0.000047  0.000044  0.000050  0.000051  0.000051  0.000052
6    0.000044  0.000050  0.000051  0.000051  0.000052  0.000047
7    0.000050  0.000051  0.000051  0.000052  0.000047  0.000047
8    0.000051  0.000051  0.000052  0.000047  0.000047  0.000043
9    0.000051  0.000052  0.000047  0.000047  0.000043  0.000038
..        ...       ...       ...       ...       ...       ...
392  0.000067  0.000057  0.000046  0.000037  0.000039  0.000035
393  0.000057  0.000046  0.000037  0.000039  0.000035  0.000043
394  0.000046  0.000037  0.000039  0.000035  0.000043  0.000044
395  0.000037  0.000039  0.000035  0.000043  0.000044  0.000044
396  0.000039  0.000035  0.000043  0.000044  0.000044  0.000044

[392 rows x 6 columns]
(392, 6)

```

**Skenario 2 — 10 Hari Sebelumnya (lag = 10)**

```python
supervised_df10 = create_supervised(df['NO2_filled'], n_lag=10)

print(supervised_df10)
print(supervised_df10.shape)
```

Contoh Output:

```
     NO2(t-10)  NO2(t-9)  NO2(t-8)  NO2(t-7)  NO2(t-6)  NO2(t-5)  NO2(t-4)  \
10    0.000047  0.000044  0.000050  0.000051  0.000051  0.000052  0.000047   
11    0.000044  0.000050  0.000051  0.000051  0.000052  0.000047  0.000047   
12    0.000050  0.000051  0.000051  0.000052  0.000047  0.000047  0.000043   
13    0.000051  0.000051  0.000052  0.000047  0.000047  0.000043  0.000038   
14    0.000051  0.000052  0.000047  0.000047  0.000043  0.000038  0.000036   
..         ...       ...       ...       ...       ...       ...       ...   
392   0.000071  0.000069  0.000068  0.000066  0.000065  0.000067  0.000057   
393   0.000069  0.000068  0.000066  0.000065  0.000067  0.000057  0.000046   
394   0.000068  0.000066  0.000065  0.000067  0.000057  0.000046  0.000037   
395   0.000066  0.000065  0.000067  0.000057  0.000046  0.000037  0.000039   
396   0.000065  0.000067  0.000057  0.000046  0.000037  0.000039  0.000035   

     NO2(t-3)  NO2(t-2)  NO2(t-1)    NO2(t)  
10   0.000047  0.000043  0.000038  0.000036  
11   0.000043  0.000038  0.000036  0.000033  
12   0.000038  0.000036  0.000033  0.000031  
13   0.000036  0.000033  0.000031  0.000035  
14   0.000033  0.000031  0.000035  0.000039  
..        ...       ...       ...       ...  
392  0.000046  0.000037  0.000039  0.000035  
393  0.000037  0.000039  0.000035  0.000043  
394  0.000039  0.000035  0.000043  0.000044  
395  0.000035  0.000043  0.000044  0.000044  
396  0.000043  0.000044  0.000044  0.000044  

[387 rows x 11 columns]
(387, 11)
```
### 3.3 Pelatihan Model dan Evaluasi

Model KNN Regression dilatih menggunakan pembagian data **80% training dan 20% testing** secara kronologis (tidak diacak) untuk menjaga urutan temporal data.

Evaluasi dilakukan menggunakan tiga metrik:

| Metrik | Rumus | Keterangan |
|--------|-------|-----------|
| **RMSE** | $\sqrt{\frac{1}{n}\sum_{i=1}^{n}(y_i - \hat{y}_i)^2}$ | Rata-rata besar kesalahan prediksi |
| **R²**   | $1 - \frac{\sum(y_i - \hat{y}_i)^2}{\sum(y_i - \bar{y})^2}$ | Proporsi variasi data yang dijelaskan model |
| **MAPE** | $\frac{1}{n}\sum_{i=1}^{n}\left\|\frac{y_i - \hat{y}_i}{y_i}\right\| \times 100\%$ | Kesalahan rata-rata dalam persentase |

```python
from sklearn.neighbors import KNeighborsRegressor
from sklearn.model_selection import train_test_split
from sklearn.metrics import mean_squared_error, r2_score
import numpy as np

def MAPE(y_true, y_pred):
    y_true, y_pred = np.array(y_true), np.array(y_pred)
    nonzero = y_true != 0
    return np.mean(np.abs((y_true[nonzero] - y_pred[nonzero]) / y_true[nonzero])) * 100

def train_knn(df_supervised, model_name=""):
    X = df_supervised.drop(columns=['NO2(t)']).values
    y = df_supervised['NO2(t)'].values

    # Split kronologis 80:20
    X_train, X_test, y_train, y_test = train_test_split(
        X, y, test_size=0.2, shuffle=False
    )

    knn = KNeighborsRegressor(n_neighbors=5)
    knn.fit(X_train, y_train)

    y_pred = knn.predict(X_test)

    mse  = mean_squared_error(y_test, y_pred)
    rmse = np.sqrt(mse)
    r2   = r2_score(y_test, y_pred)
    mape = MAPE(y_test, y_pred)

    print(f"\n=== {model_name} ===")
    print(f"Train Size : {len(X_train)}  —  Test Size : {len(X_test)}")
    print(f"RMSE       : {rmse:.6f}")
    print(f"R² Score   : {r2:.4f}")
    print(f"MAPE       : {mape:.4f}%")

    return knn, y_test, y_pred

# Pelatihan kedua skenario
knn_5,  y_test_5,  y_pred_5  = train_knn(supervised_df5,  "KNN - 5 Hari Sebelumnya")
knn_10, y_test_10, y_pred_10 = train_knn(supervised_df10, "KNN - 10 Hari Sebelumnya")
```

Output:

```

=== KNN - 5 Hari Sebelumnya ===
Train Size: 313 — Test Size: 79
RMSE: 0.000008
R² Score: 0.4535
MAPE: 10.2991%

=== KNN - 10 Hari Sebelumnya ===
Train Size: 309 — Test Size: 78
RMSE: 0.000010
R² Score: 0.0657
MAPE: 13.6031%

```

### 3.4 Visualisasi Hasil Prediksi

Grafik perbandingan antara nilai aktual dan prediksi ditampilkan untuk masing-masing skenario:

**Skenario 1 — 5 Hari Sebelumnya**

```python
import matplotlib.pyplot as plt

plt.figure()
plt.plot(np.arange(len(y_test_5)), y_test_5, label="Actual")
plt.plot(np.arange(len(y_pred_5)), y_pred_5, label="Predicted")
plt.title("KNN Regression - 5 Hari Sebelumnya")
plt.xlabel("Sample Index")
plt.ylabel("NO2 Value")
plt.legend()
plt.show()
```

![Visualisasi Outlier](img/5day.png)
**Skenario 2 — 10 Hari Sebelumnya**

```python
plt.figure()
plt.plot(np.arange(len(y_test_10)), y_test_10, label="Actual")
plt.plot(np.arange(len(y_pred_10)), y_pred_10, label="Predicted")
plt.title("KNN Regression - 10 Hari Sebelumnya")
plt.xlabel("Sample Index")
plt.ylabel("NO2 Value")
plt.legend()
plt.show()
```

![Visualisasi Outlier](img/10day.png)
---

## 4. Kesimpulan

Berdasarkan seluruh proses yang telah dilakukan, beberapa poin penting dapat disimpulkan:

1. **Pra-pemrosesan merupakan tahap terpenting** dalam proyek ini. Data satelit Sentinel-5P mengandung banyak missing value. Penanganan menggunakan interpolasi linear terbukti efektif dalam menghasilkan deret waktu yang bersih dan kontinu.

2. **Deteksi outlier dengan metode IQR** berhasil mengidentifikasi nilai-nilai ekstrem yang berpotensi mengganggu performa model. Penggantian outlier menggunakan interpolasi linear dipilih agar urutan temporal data tidak terputus.

3. **Pemilihan jumlah lag berpengaruh terhadap performa model.** Berdasarkan uji korelasi, lag yang lebih pendek (t-1 hingga t-5) memiliki hubungan yang lebih kuat dengan nilai target. Skenario 5 hari sebelumnya diperkirakan memberikan performa yang lebih baik dibandingkan skenario 10 hari, mengingat penambahan fitur yang lemah korelasinya dapat memperburuk akurasi pada model berbasis jarak seperti KNN.

## 5. File drive
Semua hasil file yang dihasilkan oleh program diatas dapat diakses melalui file drive berikut ini: [Folder drive](https://drive.google.com/drive/folders/1JXbwV_nE-lEU5Fd_m8JGciYr8aOjdz6R?usp=sharing)


