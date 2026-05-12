# Analisis Data Menggunakan Decision Tree dengan Metode Gain Ratio pada KNIME
## 1. Decision Tree
Decision Tree merupakan salah satu cara data processing dalam memprediksi masa depan dengan cara membangun klasifikasi atau regresi model dalam bentuk struktur pohon.
Decision Tree juga berguna untuk dieksplorasi data, menemukan hubungan antara sejumlah calon variabel input dengan sebuah variabel target. Pohon keputusan eksplorasi data dan pemodelan yang salah langkah pertama yang sangat baik dalam proses pemodelan yang digunakan sebagai model akhir untuk beberapa teknik lainnya.
## 2. Struktur Decision Tree
Struktur decision tree  adalah model prediksi hierarkis yang berbentuk seperti pohon terbalik, terdiri dari simpul akar (root node), simpul internal (internal/decision node), cabang (branch), dan simpul daun (leaf node).
**Komponen Utama Struktur Decision Tree**
1. Simpul Akar (Root Node): Node paling atas yang mewakili pertanyaan utama atau masalah yang ingin dipecahkan. Ini adalah titik awal analisis yang berisi atribut paling signifikan.
2. Simpul Internal (Decision Node): Cabang dari akar atau node lain, yang mewakili pengujian atau keputusan berdasarkan atribut tertentu.
3. Cabang (Branch): Jalur yang menghubungkan antar node, mewakili kemungkinan jawaban atau hasil dari keputusan.
4. Simpul Daun (Leaf Node/Terminal Node): Node akhir yang tidak memiliki cabang lagi, yang memuat hasil akhir, keputusan, atau kelas target.

Decision Tree bekerja dengan:
1. Memilih atribut terbaik
2. Membagi data berdasarkan atribut tersebut
3. Mengulangi proses pembagian
4. Berhenti ketika data sudah homogen

## 3. Ukuran dalam Membangun Decision Tree

Dalam Decision Tree, ada tiga ukuran utama yang sering dipakai:
**1.Entropy**
Digunakan untuk mengukur tingkat "kekacauan" atau ketidakmurnian (impurity) dalam suatu kumpulan data.
Rumus Entropy:

$Entropy(S) = - \sum_{i=1}^{n} p_i \log_2 p_i$

Keterangan:

$S$ = dataset
$p_i$ = proporsi kelas ke-i

Rumus Weighted Entropy:

$Entropy(S, A) = \sum_{j=1}^{v} \frac{|S_j|}{|S|} \cdot Entropy(S_j)$

Keterangan:
$A$ = atribut
$S_j$ = subset ke-j
$v$ = jumlah nilai atribut


**2. Information Gain**
   Information Gain adalah ukuran seberapa banyak Entropy berkurang setelah kita membagi data berdasarkan fitur tertentu. Fitur dengan Information Gain tertinggi akan dipilih sebagai akar atau cabang.

Rumus:
$Gain(S, A) = Entropy(S) - Entropy(S, A)$

**3. Gain Ratio**
Gain Ratio adalah penyempurnaan dari Information Gain. Untuk  mengatasi kelemahan Information Gain dengan menerapkan "penalti" pada fitur yang punya terlalu banyak cabang (nilai unik).

rumus:
$GainRatio(S, A) = \frac{Gain(S, A)}{SplitInfo(S, A)}$
**Konsep Gain Ratio**
Gain Ratio membandingkan Information Gain dengan Split Information. Jika sebuah fitur punya terlalu banyak cabang, nilai pembaginya akan besar, sehingga skor akhirnya (Gain Ratio) mengecil.
* Information Gain (Pembilang)
* Split Information (Penyebut)
  Menghitung seberapa banyak fitur tersebut memecah data. Semakin banyak cabangnya, semakin besar nilai SplitInfo.
  $SplitInfo(S, A) = - \sum_{j=1}^{v} \frac{|S_j|}{|S|} \log_2 \left(\frac{|S_j|}{|S|}\right)$

  `Alur Konsep Gain Ratio:`
  _Entropy tinggi -> Hitung Information Gain -> Hitung Split Information -> Hitung Gain Ratio -> Pilih atribut dengan Gain Ratio terbesar_.

## 4. Dataset 
Dataset yang digunakan dalam tugas ini adalah Mushroom Dataset yang berisi data karakteristik berbagai jenis jamur untuk proses klasifikasi apakah jamur tersebut dapat dimakan (edible) atau beracun (poisonous). Dataset ini memiliki 8124 data dengan 23 atribut, di mana atribut target yang digunakan adalah kolom class. Nilai e pada kolom class menunjukkan jamur dapat dimakan, sedangkan nilai p menunjukkan jamur beracun. Setiap data memiliki beberapa karakteristik fisik seperti bentuk tudung jamur (cap-shape), warna tudung (cap-color), bau (odor), ukuran insang (gill-size), dan habitat jamur. Dataset ini dipilih karena memiliki struktur data yang lengkap, bersih, dan cocok digunakan untuk metode klasifikasi menggunakan algoritma Decision Tree dengan ukuran Gain Ratio pada KNIME Analytics Platform.

Dalam pembangunan pohon keputusan digunakan metode Gain Ratio sebagai ukuran pemilihan atribut terbaik. Gain Ratio dipilih karena dataset mushroom memiliki banyak atribut kategorikal dengan jumlah kategori yang berbeda-beda. Jika hanya menggunakan Information Gain, atribut dengan jumlah kategori lebih banyak cenderung lebih diutamakan sehingga dapat menyebabkan bias dalam pembentukan pohon keputusan. Oleh karena itu, Gain Ratio digunakan untuk mengurangi bias tersebut dengan mempertimbangkan nilai pembagian data (Split Information), sehingga atribut yang dipilih benar-benar memberikan pembagian data yang paling optimal dan menghasilkan model klasifikasi yang lebih akurat.

Sumber dataset : [Data yang digunakan](https://www.kaggle.com/datasets/uciml/mushroom-classification)

## 5. Implementasi Decision Tree di Knime
![original image](https://cdn.mathpix.com/snip/images/dmcgEaDPjlKkbWpitHjfbvUIJ9x7lNwYvfA8sWTUirk.original.fullsize.png)

**1. CSV Reader**
Di dalam CSV reader kita harus meng-import data file csv yang di dapatkan melalui kaggle tadi, lalu atur berdasarkan pemisah csv yang digunakan.
![original image](https://cdn.mathpix.com/snip/images/JwIPCSx5ZIF2XOSuYu980fCPAPokctvDN5_mS0aFJ-U.original.fullsize.png)

![original image](https://cdn.mathpix.com/snip/images/xEeo5nFuwApmJ3HWnKZ4ZVd1-UFAjy13xqC1raIR3so.original.fullsize.png)

Jika pemisah yang digunakan adlah comma, maka kita set delimitter column nya adalah comma

Hasil:



**2. Table Partitioner**
Data dibagi menjadi data training dan testing agar model dapat dilatih dan diuji performanya. Idealnya dalam dunia Machine Learning pembagian data training dan testing adalah 80:20, 80 untuk training dan 20 untuk testing.
![original image](https://cdn.mathpix.com/snip/images/BKX0QAofoI5Ha2CLcADSV-Tnrt3eoo6lMmb43l8bfSk.original.fullsize.png)

Maka kita set nilai relative nya menjadi 80, dengan metode random, maksud dari random adalah setiap kali kita  running kembali, maka data training dan testing akan di random kembali dengan relative size yang tetap

Hasil:
Partisi 1(training):

![original image](https://cdn.mathpix.com/snip/images/yoA2yovUfDW9WnsgQbNcFDoVmDc5tKOOejEzpxO-1K4.original.fullsize.png)

Partisi 2(testing):

![original image](https://cdn.mathpix.com/snip/images/W28Cn8algoKBSQf_Qvxf5U3qnQ-AKwYqHkIVv3XSHmw.original.fullsize.png)

**3. Color Manager**
Node color manager tidak memiliki hbungan apa apa dengan penggunaan node decision tree kita nantinya, Node ini  digunakan untuk kita memberi warna pada kolom class, agar terlihat lebih mudah dibedakan nantinya. Implementasinya seperti berikut ini:

![original image](https://cdn.mathpix.com/snip/images/kINzqwvssvERiyWKmEhjsMmo0-iKp9ToEtJm9Vf8V98.original.fullsize.png)

Pertama pilih kolom yang ingin diberi warna, dalam kasus ini kita gunakan kolom sebagai class agar lebih mudah di cirikan. Selanjutnya isi dari class tersebut kita bedakan warna nya, contoh untuk e atau edible kita gunakan hijau, karena aman. Lalu untuk p atau poisonous kita gunakan merah karena tidak aman

Hasilnya:

![original image](https://cdn.mathpix.com/snip/images/Voj_rO7fwqYnHWV8wiluIOPQBAlbYMVjF3sNRI3JaOw.original.fullsize.png)


**4. Color Appender**
Node ini sama saja dengan color manager, hanya saja jika color manager tadi cuma bisa digunakan di data training, maka kita bawa settingan color manager data training tadi untuk ke data testing, itu akan lebih menghemat penggunaan node

Hasil:

![original image](https://cdn.mathpix.com/snip/images/EfZbKrrUFHT7nuA_pIUaixpUSUEQ7WuQYWbpbg5ysWU.original.fullsize.png)

**5. Decision Tree learner**
Selanjutnya, node decision tree learner. Node ini digunakan untuk kita meng-build model decision tree yang nantinya kita gunakan

![original image](https://cdn.mathpix.com/snip/images/9CLV-QJkO1eqAgu6MTRrb8i3IHpwelD5JtJeBIV9_gY.original.fullsize.png)

- Class column
ini digunakan untuk kita memasukkan kolom class yyang sudah ditentukan. Dan model decision tree akan memprediksi nilai dari kolom inii
- Quality measure
Ada 2 quality measure pada decision tree, yaitu Gini Index dan Gain Ratio. 
Pada dasarnya quality measure adalah metode yang digunakan untuk men-split fitur nantinya.

  1. Gini Index
     Digunakan untuk mengukur impurity atau ketidakmurnian data. Semakin kecil GINI artinya semakin murni data tersebut

  2. Gain Ratio
     Digunakan untuk mengukur information gain dari suatu split. Metode ini lebih cocok digunakan untuk data kategori

- Pruning method
Pruning  (memangkas) ini adalah teknik dalam decision tree untuk meringkas atau membuat tree nya tida overfitting. Dalam knime ada 2 metode

  1. No Pruning
     Artinya tidak ada pemangkasan disini, tree bisa sangat dalam bahka sangat kompleks. Hanya cocok digunakan di dataseet kecil.

  2. MDL
     Minimum Description Length, metode otomatis untuk memangkas cabang yang tidak penting, memuat model lebih sederhana dan generalisasi lebih bagus

- Minimum number of records per node
Ini adalah nilai minimal sebuah data agar dapat di split, contohnya jika kita set 1000 artinya  jika node cuma punya <1000 data, maka node tidak boleh di split lagi

- Number of records to store per view
Ini hanya meng-set  jumlah data yang disimpan untuk visualisasi tree nantinya. Jika terlalu banyak, maka UI bisa berat

- Number threads
Untuk set  jumlah thread CPU yang dipakai komputer kita

**6. Decission Tree Predictor**
Pada node ini, model decision tree yang sudah di build, akan digunakan untuk memprediksi data dari testing. Lalu akan menciptakan kolom baru dengan nama PREDICT(class) dimana itu adalah hasil prediksi dari decision tree predictor ini

**7.Scorer**
Node scorer digunakan untuk melihat hasil confussion matrix, hasil precision prediksi, recall dan banyaak hal lain nya

![original image](https://cdn.mathpix.com/snip/images/l8LIL08JGDv70t-FLQm-HYurB1uG5r_ZIuYN7rHtHBA.original.fullsize.png)

Maka dari itu kita set first  column adalah kolom class asli pada data testing, untuk kita hitung ke akuratan nya dengan second column yaitu class dari hasil prediksi decision tree learner. Sorting dibawahnya hanya digunaka untuk kita mau mengurutkan seperti apa datanya

Hasil:

Confussion matrix:

![original image](https://cdn.mathpix.com/snip/images/HtmTwfm9xazxuXPr6fE3WORz2Il52QcMh-N14B94rmo.original.fullsize.png)

Berdasarkan confussion matrix diatas, decision tree berhasil menebak class dengan nilai P(poisonous) adalah 759 data, dan salah dengan 10 data lain nya. Lalu pada class dengan nilai e(edible) 856 dengan nilai salah nya adalah 0, artinya seluruh class e dapat di prediksi dengan benar.

Accuracy statistics:

![original image](https://cdn.mathpix.com/snip/images/MTNznfflmfoo-9IRTmyeFgiWXdxc13dMGOc9dgKjr1Q.original.fullsize.png)

![original image](https://cdn.mathpix.com/snip/images/3XSq-EitAc7rE_I6wqRmDI1XbKZNRckaIQ3oECbOBY8.original.fullsize.png)

![original image](https://cdn.mathpix.com/snip/images/9Arl7oL9n3DEEFpA50kRuNsXD-rxAohjMAjn91D51CI.original.fullsize.png)

- Recall: mengukur berdasarkan data  asli, ada berapa data yang ditemukan
- Precision: Menghitung seberapa presisi prediksi data berdasarkan class nya tadi, jika 1 maka sangat  presisi
- Specifity: Mengukur seberapa baik model mengenali class selain target nya
- F-measure: Bisa disebut juga F1 score, dimana ini adalah gabungan dari precision dan recall
- Accuracy: Ini mengukur seberapa akurat prediksi data secara keseluruhan nya