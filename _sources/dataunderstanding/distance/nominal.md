
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
