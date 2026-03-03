# Distance
Jarak atau _distance_ merupakan ukuran untuk mengetahui seberapa mirip atau seberapa berbeda antara dua buah data, semakin kecil nilai jarak maka data semakin mirip begitupun sebaliknya.
## Jarak Atribut Numerik
### Manhattan Distance
Manhattan Distance adalah metode untuk menghitung jarak antara dua titik dengan menjumlahkan selisih absolut tiap dimensi. Manhattan Distance dirumuskan sebagai berikut :

$$
    d(\mathbf{x}, \mathbf{y}) = \sum_{i=1}^{n} |x_i - y_i|
$$
Dimana
- $\mathbf{x}$ merupakan data pertama
- $\mathbf{y}$ merupakan data kedua
- $x_i$ merupakan fitur ke-i dari data pertama
- $y_i$ merupakan fitur ke-i dari data kedua

### Euclidean Distance 
Euclidean distance adalah ukuran jarak “lurus” atau jarak garis lurus antara dua titik dalam ruang n-dimensi. Ini adalah generalisasi jarak Pythagoras ke dimensi lebih tinggi. Euclidean Distance dirumuskan sebagai berikut:

$$
d(\mathbf{p}, \mathbf{q}) = \sqrt{\sum_{i=1}^{n} (p_i - q_i)^2}
$$
Dimana
- $\mathbf{p}$ merupakan data pertama
- $\mathbf{q}$ merupakan data kedua
- $p_i$ merupakan fitur ke-i dari data pertama
- $q_i$ merupakan fitur ke-i dari data kedua

## Jarak Atribut Binary
### Biner Simetris
Biner simetris adalah atribut biner di mana kedua nilai (0 dan 1) sama pentingnya. Nilai 1 tidak lebih penting dari 0, begitupun sebaliknya. Rumus menghitung jarak data dengan atribut biner simetris sebagai berikut:

$$
D = \frac{r + s}{q + r + s + t}
$$

Dimana:
- $\mathbf{q}$ adalah jumlah atribut (1,1) 
- $\mathbf{r}$ adalah jumlah atribut (1,0) 
- $\mathbf{s}$ adalah jumlah atribut (0,1) 
- $\mathbf{t}$ adalah jumlah atribut (0,0) 

|      -       |$\mathbf{Y}=1$|$\mathbf{Y}=0$|
|:------------:|:------------:|:------------:|
|$\mathbf{X}=1$| $\mathbf{q}$ | $\mathbf{r}$ |
|$\mathbf{X}=0$| $\mathbf{s}$ | $\mathbf{t}$ |

### Biner Asimetris
Biner asimetris adalah atribut biner di mana nilai 1 lebih penting daripada nilai 0. Rumus menghitung jarak data dengan atribut biner asimetris sebagai berikut:

$$
D = \frac{r + s}{q + r + s }
$$
## Jarak Atribut Kategorikal
Data kategorikal adalah data berbentuk kategori/label (misalnya: warna, jurusan, status). menghitung jarak data dengan atribut kategorikal sebagai berikut:

$$
D = \frac{u}{p}
$$
Dimana:
- $\mathbf{u}$ adalah jumlah atribut yang berbeda
- $\mathbf{p}$ adalah jumlah sumua atribut (kategorikal)
## Jarak Atribut Ordinal
Data ordinal adalah data kategorikal yang memiliki urutan/tingkatan, tetapi jarak antar tingkatannya tidak pasti sama.
### Langkah Menghitung Jarak Ordinal
1. Ubah ke ranking numerik

    Misalkan ada $\mathbf{M}$ tingkat kategori, maka setiap kategori diberi ranking : $1, 2, 3, ...,\mathbf{M}$.
2. Normalisasi ke interval [0.0 - 1.0]
    
    Untuk atribut ke-$\mathbf{k}$:

    $$
        Z_k = \frac{r_k - 1}{M - 1}
    $$

    Keterangan:
    - $r_k$ merupakan ranking kategori
    - $M$ merupakan jumlah level kategori
    - $Z_k$ merupakan nilai hasil normalisasi

3. Hitung Jarak
    Setelah dinormalisasi, gunakan jarak numerik biasa (misalnya Manhattan atau Euclidean).

    Manhattan Distance:

    $$
        d(\mathbf{x}, \mathbf{y}) = \sum_{i=1}^{n} |x_i - y_i|
    $$
    Ecluidean Distance:
    
    $$
        d(\mathbf{p}, \mathbf{q}) = \sqrt{\sum_{i=1}^{n} (p_i - q_i)^2}
    $$