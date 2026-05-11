# Decision Tree Heart Disease Classification

## Dataset

Dataset yang digunakan pada penelitian ini adalah `Heart Disease Dataset`. Dataset ini digunakan untuk melakukan klasifikasi apakah seseorang memiliki penyakit jantung atau tidak berdasarkan kondisi medis pasien.

Dataset terdiri dari beberapa atribut medis seperti umur, tekanan darah, kadar kolesterol, detak jantung, dan lain sebagainya yang digunakan untuk memprediksi kemungkinan penyakit jantung.

Link Dataset : https://www.kaggle.com/datasets

Dataset ini memiliki beberapa fitur numerik dan kategorikal dengan label target berupa:
- `0` = Tidak memiliki penyakit jantung
- `1` = Memiliki penyakit jantung

Berikut beberapa fitur utama pada dataset:

| No | Nama Fitur | Deskripsi |
|---|---|---|
| 1 | age | Umur pasien |
| 2 | sex | Jenis kelamin |
| 3 | cp | Tipe nyeri dada |
| 4 | trestbps | Tekanan darah saat istirahat |
| 5 | chol | Kadar kolesterol |
| 6 | fbs | Gula darah puasa |
| 7 | restecg | Hasil elektrokardiografi |
| 8 | thalach | Detak jantung maksimum |
| 9 | exang | Angina akibat olahraga |
| 10 | oldpeak | Depresi ST akibat olahraga |
| 11 | slope | Kemiringan segmen ST |
| 12 | ca | Jumlah pembuluh darah utama |
| 13 | thal | Kelainan darah thalassemia |
| 14 | target | Hasil klasifikasi penyakit jantung |

---

# Implementasi Pada KNIME

Workflow ini dirancang menggunakan aplikasi KNIME untuk membangun model klasifikasi Decision Tree guna memprediksi penyakit jantung berdasarkan data medis pasien.

![Workflow](../img/work-flow-tree.png)

---

## Partitioning

Tahap pertama setelah membaca dataset adalah melakukan pembagian data menggunakan node `Partitioning`.

Data dibagi menjadi:
- 70% data training
- 30% data testing

Tujuan pembagian data adalah agar model dapat dilatih menggunakan data training dan diuji menggunakan data testing.

![Partitioning](../img/partitioning.png)

---

## Decision Tree Learner

Node `Decision Tree Learner` digunakan untuk membangun model pohon keputusan berdasarkan data training.

Pada penelitian ini digunakan:
- Split Criterion : `Gain Ratio`

![Decision Tree Learner](../img/learner.png)

---

# Konsep Gain Ratio

Decision Tree memilih atribut terbaik berdasarkan nilai `Gain Ratio` tertinggi.

Rumus Gain Ratio:

$$
GainRatio(A) = \frac{Gain(A)}{SplitInfo(A)}
$$

---

## Entropy

Entropy digunakan untuk mengukur tingkat ketidakpastian data.

$$
Entropy(S) = - \sum p_i \log_2 p_i
$$

Keterangan:
- Semakin kecil entropy, maka data semakin murni
- Entropy = 0 berarti seluruh data berada pada satu kelas

---

## Information Gain

Information Gain digunakan untuk mengukur seberapa baik suatu atribut membagi data.

$$
Gain(S,A) = Entropy(S) - \sum \frac{|S_v|}{|S|}Entropy(S_v)
$$

---

## Split Information

Split Information digunakan untuk mengukur penyebaran data hasil split.

$$
SplitInfo(A) = - \sum \frac{|S_v|}{|S|}\log_2 \frac{|S_v|}{|S|}
$$

---

## Gain Ratio

Gain Ratio merupakan hasil pembagian Information Gain dengan Split Information.

$$
GainRatio(A) = \frac{Gain(A)}{SplitInfo(A)}
$$

Atribut dengan nilai Gain Ratio tertinggi akan dipilih sebagai root node.

---

# Perhitungan Entropy Root

Misalnya:
- Jumlah kelas target 0 = xxx
- Jumlah kelas target 1 = xxx
- Total data = xxx

Maka entropy root dapat dihitung menggunakan rumus entropy.

---

# Pembentukan Tree

Setelah nilai Gain Ratio dihitung untuk seluruh atribut, atribut dengan nilai tertinggi dipilih menjadi root node.

Proses ini dilakukan secara rekursif sampai:
- seluruh node menjadi pure
- atau tidak ada atribut tersisa

Hasil pohon keputusan dapat dilihat menggunakan node `Decision Tree View`.

![Tree](../img/tree.png)

---

# Decision Tree Predictor

Node `Decision Tree Predictor` digunakan untuk melakukan prediksi terhadap data testing menggunakan model yang telah dibuat.

![Predictor](../img/predictor.png)

---

# Confusion Matrix

Hasil prediksi dievaluasi menggunakan confusion matrix.

![Confusion Matrix](../img/confusion-tree.png)

---

# Akurasi Model

Akurasi model diperoleh menggunakan node `Scorer`.

![Accuracy](../img/accuracy-tree.png)

---

# Kesimpulan

Berdasarkan hasil implementasi menggunakan metode Decision Tree dengan Gain Ratio, model mampu melakukan klasifikasi penyakit jantung dengan tingkat akurasi yang cukup baik.

Metode Decision Tree dapat membantu dalam proses analisis data medis karena mampu menghasilkan aturan klasifikasi yang mudah dipahami dalam bentuk pohon keputusan.