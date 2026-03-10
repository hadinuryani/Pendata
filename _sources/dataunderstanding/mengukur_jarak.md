---
jupytext:
  formats: md:myst
  text_representation:
    extension: .md
    format_name: myst
    format_version: 0.13
    jupytext_version: 1.11.5
kernelspec:
  display_name: Python 3
  language: python
  name: python3
---

# Mengukur Jarak Antar Data

Mengukur jarak (dissimilarity) antar dua objek data dari dataset Titanic berdasarkan tipe datanya masing-masing.

```{code-cell}
:tags: [hide-input]
import pandas as pd
import numpy as np

df = pd.read_csv("train.csv")
df.head(5)
```

---

## 1. Binary

Menghitung jarak data binary antara dua sampel dari data di atas.

Dari data di atas, fitur dengan tipe data binary adalah `Survived`. Fitur ini hanya memiliki dua nilai yaitu `0` (tidak selamat) dan `1` (selamat).

Karena `Survived` merupakan **asymmetric binary** (nilai 1 lebih bermakna daripada 0), maka digunakan **Jaccard Distance**:

$$d(i,j) = \frac{r + s}{q + r + s}$$

- **q** = keduanya bernilai 1
- **r** = i = 0, j = 1
- **s** = i = 1, j = 0

```{code-cell}
:tags: [hide-input]
from scipy.spatial.distance import jaccard

binary_cols = ['Survived']

p1 = df[binary_cols].iloc[0]
p2 = df[binary_cols].iloc[1]

print(f"Row 1 - Survived: {p1['Survived']}")
print(f"Row 2 - Survived: {p2['Survived']}")
print()

jaccard_distance = jaccard(p1, p2)
print(f"Jaccard Distance: {jaccard_distance:.4f}")
```

```{note}
Pada implementasi di atas, data yang digunakan adalah baris pertama dan kedua. Row 1 tidak selamat (0) dan Row 2 selamat (1), sehingga jaraknya bernilai **1.0** (maksimum / paling berbeda).
```

---

## 2. Nominal (Kategorikal)

Menghitung jarak data nominal antara dua sampel dari data di atas.

Fitur dengan tipe data nominal adalah `Sex` dan `Embarked`. Keduanya berupa kategori tanpa urutan yang berarti.

Digunakan **Simple Matching**:

$$d(i,j) = \frac{p - m}{p}$$

- **p** = jumlah atribut nominal
- **m** = jumlah atribut yang nilainya sama

```{code-cell}
:tags: [hide-input]
nominal_cols = ['Sex', 'Embarked']

p1 = df.loc[0, nominal_cols].values
p2 = df.loc[1, nominal_cols].values

print(f"Row 1 → Sex: {p1[0]}, Embarked: {p1[1]}")
print(f"Row 2 → Sex: {p2[0]}, Embarked: {p2[1]}")
print()

cocok = [str(a) == str(b) for a, b in zip(p1, p2)]
for attr, a, b, c in zip(nominal_cols, p1, p2, cocok):
    print(f"  {attr}: {a} vs {b} → {'cocok ✅' if c else 'beda ❌'}")

m = sum(cocok)
p = len(nominal_cols)
distance = (p - m) / p

print()
print(f"m (cocok) = {m}, p (total atribut) = {p}")
print(f"Simple Matching Distance = ({p} - {m}) / {p} = {distance:.4f}")
```

```{note}
Pada implementasi di atas, data yang digunakan adalah baris pertama dan kedua. Sex berbeda (male vs female) dan Embarked berbeda (S vs C), sehingga jaraknya bernilai **1.0**.
```

---

## 3. Ordinal

Menghitung jarak data ordinal antara dua sampel dari data di atas.

Fitur dengan tipe data ordinal adalah `Pclass` (kelas penumpang: 1, 2, 3). Nilainya memiliki **urutan/peringkat** namun jarak antar kelas tidak tentu sama.

Langkah perhitungan:

1. Gunakan ranking yang sudah ada (1, 2, 3)
2. Normalisasi ke skala [0, 1]: $z_{if} = \dfrac{r_{if} - 1}{M_f - 1}$, dengan $M_f = 3$
3. Hitung selisih: $d = |z_i - z_j|$

```{code-cell}
:tags: [hide-input]
Mf = 3  # jumlah rank: kelas 1, 2, 3

r1 = df.loc[0, 'Pclass']
r2 = df.loc[1, 'Pclass']

z1 = (r1 - 1) / (Mf - 1)
z2 = (r2 - 1) / (Mf - 1)

distance = abs(z1 - z2)

print(f"Row 1 → Pclass = {r1} → z = ({r1}-1)/(3-1) = {z1:.4f}")
print(f"Row 2 → Pclass = {r2} → z = ({r2}-1)/(3-1) = {z2:.4f}")
print()
print(f"Ordinal Distance = |{z1:.4f} - {z2:.4f}| = {distance:.4f}")
```

```{note}
Pada implementasi di atas, data yang digunakan adalah baris pertama dan kedua. Pclass Row 1 = 3 (kelas bawah) dan Pclass Row 2 = 1 (kelas atas), sehingga setelah dinormalisasi jaraknya bernilai **1.0** (paling berbeda).
```

---

## 4. Numerik

Menghitung jarak data numerik antara dua sampel dari data di atas.

Fitur dengan tipe data numerik adalah `Age` dan `Fare`. Sebelum dihitung jaraknya, data dinormalisasi terlebih dahulu menggunakan **Z-score**:

$$z = \frac{x - \mu}{\sigma}$$

Kemudian dihitung **Euclidean Distance**:

$$d(i,j) = \sqrt{\sum_{f=1}^{p}(z_{if} - z_{jf})^2}$$

```{code-cell}
:tags: [hide-input]
from scipy.spatial.distance import euclidean

numeric_cols = ['Age', 'Fare']

# Normalisasi Z-score menggunakan mean & std seluruh dataset
mean = df[numeric_cols].mean()
std  = df[numeric_cols].std()

p1_raw = df.loc[0, numeric_cols]
p2_raw = df.loc[1, numeric_cols]

p1_z = (p1_raw - mean) / std
p2_z = (p2_raw - mean) / std

print("Statistik dataset:")
for col in numeric_cols:
    print(f"  {col}: mean={mean[col]:.2f}, std={std[col]:.2f}")

print()
print("Nilai setelah Z-score normalisasi:")
for col in numeric_cols:
    print(f"  Row 1 → {col}: {p1_raw[col]} → z = {p1_z[col]:.4f}")
    print(f"  Row 2 → {col}: {p2_raw[col]} → z = {p2_z[col]:.4f}")

print()
distance = euclidean(p1_z, p2_z)
print(f"Euclidean Distance = {distance:.4f}")
```

```{note}
Pada implementasi di atas, data yang digunakan adalah baris pertama dan kedua. Perbedaan Age (22 vs 38) dan Fare (7.25 vs 71.28) yang cukup besar menghasilkan jarak Euclidean yang besar setelah dinormalisasi.
```

---

## 5. Campuran (Mixed)

Menggabungkan seluruh perhitungan jarak di atas menjadi satu nilai jarak untuk data dengan **tipe campuran**.

Rumus gabungan:

$$d(i,j) = \frac{\sum_{f=1}^{p} \delta_{ij}^{(f)} \cdot d_{ij}^{(f)}}{\sum_{f=1}^{p} \delta_{ij}^{(f)}}$$

- $\delta = 1$ jika atribut **tersedia** (tidak missing)
- $\delta = 0$ jika atribut **kosong/missing** → di-skip

Setiap jarak per tipe data yang sudah dihitung dijumlahkan lalu dirata-rata.

```{code-cell}
:tags: [hide-input]
from scipy.spatial.distance import jaccard, euclidean

row1 = df.iloc[0]
row2 = df.iloc[1]

# --- Binary: Survived (Jaccard) ---
b_cols = ['Survived']
d_binary = jaccard(row1[b_cols], row2[b_cols])

# --- Nominal: Sex & Embarked (Simple Matching) ---
n_cols = ['Sex', 'Embarked']
p1_n = df.loc[0, n_cols].values
p2_n = df.loc[1, n_cols].values
d_nominal = np.mean(p1_n != p2_n)

# --- Ordinal: Pclass ---
Mf = 3
z_pclass1 = (row1['Pclass'] - 1) / (Mf - 1)
z_pclass2 = (row2['Pclass'] - 1) / (Mf - 1)
d_ordinal = abs(z_pclass1 - z_pclass2)

# --- Numerik: Age & Fare (Z-score + Euclidean) ---
num_cols = ['Age', 'Fare']
mean = df[num_cols].mean()
std  = df[num_cols].std()
p1_z = (df.loc[0, num_cols] - mean) / std
p2_z = (df.loc[1, num_cols] - mean) / std
d_numeric = euclidean(p1_z, p2_z)

# --- Campuran ---
jarak = {
    'Binary  (Survived)'     : d_binary,
    'Nominal (Sex, Embarked)': d_nominal,
    'Ordinal (Pclass)'       : d_ordinal,
    'Numerik (Age, Fare)'    : d_numeric,
}
delta = {k: 1 for k in jarak}  # semua atribut tersedia

print(f"{'Tipe Data':<30} {'δ':>4} {'Jarak':>10}")
print("-" * 47)
for k, v in jarak.items():
    print(f"{k:<30} {delta[k]:>4}   {v:>10.4f}")

numerator   = sum(delta[k] * jarak[k] for k in jarak)
denominator = sum(delta[k] for k in jarak)
d_campuran  = numerator / denominator

print("-" * 47)
print(f"\nΣ (δ × d) = {numerator:.4f}")
print(f"Σ δ       = {denominator}")
print(f"\n✅ Jarak Campuran Row 1 vs Row 2 = {numerator:.4f} / {denominator} = {d_campuran:.4f}")
```

```{note}
Pada implementasi di atas, jarak campuran dihitung dengan merata-ratakan seluruh jarak per tipe data. Hasilnya mencerminkan seberapa berbeda Row 1 (penumpang laki-laki, kelas 3, tidak selamat) dengan Row 2 (penumpang perempuan, kelas 1, selamat) secara keseluruhan.
```
