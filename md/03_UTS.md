# UTS 

## Sumber data
Dataset yang digunakan berisi mengenai data kesuburan tanah, terdapat 2000 data yang akan diolah dengan missing value di dalamnya

### Pembersihan missing value dan mengubah nilai kategori menjadi numerik
Data diolah dengan tools knime, dimana dengan menggunakan node missing value
dimana terdaoat treatment jika data tersebut integer atau float maka akan digunakan nilai rata-rata untuk missing value nya. Jika datanya adalah kategori atau string, maka akan digunakan value paling besar yang muncul disana

![original image](https://cdn.mathpix.com/snip/images/p92VpC-sFyIaPopALc3KTaApPuOgAPlRVZ4AaJll8dY.original.fullsize.png)

Selanjutnya data dengan tipe data kategori yaitu tekstur tanah akan diubah menjadi numerik dengan node one to many

![original image](https://cdn.mathpix.com/snip/images/jUTsJ0Z6XlU4xjnYXCIlDxSMKiukwVplhDcOh4rdNEw.original.fullsize.png)

### Pembersihan kolom, partitioner data, normalisasi
Selanjutnya, data kolom yang tidak digunakan akan dibersihkan dengan node column filter, lalu data akan dipartisi untuk dibagi antara data testing dan data training dengan node table partitioner Dimana data training akan mengambil 1400 sample, dan data testing akan mengambil 600 sample. Langkah selanjutnya lakukan normalisasi dengan min-max normalization 

![original image](https://cdn.mathpix.com/snip/images/Bhbq3nQsX345tqcGrjWsKcVrPXm9UzT402OBmpsfSEQ.original.fullsize.png)



### Pca 
Dilakukan pca untuk mengurangi dimensi data yang dimiliki, dengan membatasi nya menjadi 4 dimensi, Nilai pca akan mengambil dari nilai normalisasi data seperti gambar berikut
![original image](https://cdn.mathpix.com/snip/images/KqIoB-t5hI-voeXaPRQv5uSfWpmfwFQfstAJmiGKTho.original.fullsize.png)

### KNN
Setelah meredudansi data menjadi 4 dimensi selanjutnya hasil PCA akan dilakukan prediksi class dengan menggunakan metode KNN (k nearest neiugbour)
![original image](https://cdn.mathpix.com/snip/images/bfTXAKgbEymlc2tByn39gewQE5JVG-b0rRdEsfacwDI.original.fullsize.png)

Hasil knn akan menunjukan dari testing data, artinya dia akan menunjukan 600 rows dengan klasifikasi baru yang dia prediksikan contoh hasil:

![original image](https://cdn.mathpix.com/snip/images/0fap8D8nn_4qyGQjipLoDBWkQ2AyoLACQDWleNz5fCk.original.fullsize.png)

Selanjutnya kita akan menghitung Acuracy, precission, Recall, F-1 Score dengan node scorer. Membandingkan class asli data/label dengan class yang diciptakan KNN. Hasil akhir:

![original image](https://cdn.mathpix.com/snip/images/LTQJGmXTd271ksbhCzKq8QzKZ08o3JQH30OYEcWA53g.original.fullsize.png)


![original image](https://cdn.mathpix.com/snip/images/JUGhzYbuOdetyN9w9teCxwMRBjR2YfLvi9_Lh_uAkLs.original.fullsize.png)

Hasil diatas terlihat bahwa nilai Accuracy, Precission, Recall, dan F-1score (dalam knime adalah F-measure) berada pada rentang 1. Yang artinya proses Knn dengan pca berjalan dengan sangat persis dan sesuai dengan class pada data awal.

### Gambar keseluruhan node knime

![original image](https://cdn.mathpix.com/snip/images/NA_lYZ8GOayJh5qP-jXqcS2_b_YTBnVX_r4m8BQP0Bo.original.fullsize.png)

