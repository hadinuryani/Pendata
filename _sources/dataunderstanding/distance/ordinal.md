
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
