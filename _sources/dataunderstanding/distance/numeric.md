
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
