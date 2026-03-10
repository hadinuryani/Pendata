
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