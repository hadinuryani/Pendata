
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
print(f"\nJarak Campuran Row 1 vs Row 2 = {numerator:.4f} / {denominator} = {d_campuran:.4f}")
```

```{note}
Pada implementasi di atas, jarak campuran dihitung dengan merata-ratakan seluruh jarak per tipe data. Hasilnya mencerminkan seberapa berbeda Row 1 (penumpang laki-laki, kelas 3, tidak selamat) dengan Row 2 (penumpang perempuan, kelas 1, selamat) secara keseluruhan.
```
