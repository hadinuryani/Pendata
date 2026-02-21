# Data preparation
# Sampel Mean

Diasumsikan data kategorikal dipetakan ke variabel acak dan membentuk sampel IID.

## Sampel Mean (Rata-rata Sampel)

Sampel mean dihitung sebagai:

μ̂ = (1/n) Σ xi

Untuk data kategorikal yang didiskritisasi:
- pî = ni / n
- ni = jumlah kemunculan kategori ke-i
- Σ ni = n

Sehingga:
μ̂ = p̂ = (p̂1, p̂2, ..., p̂m)

Artinya, mean sampel pada data kategorikal sama dengan estimasi probabilitas empiris (pmf).

---

# Matriks Kovarian (Multivariate Bernoulli)

Misalkan:
X = (A1, A2, ..., Am)ᵀ  
dengan Ai variabel Bernoulli.

## Variansi
var(Ai) = pi (1 − pi)

## Kovariansi
cov(Ai, Aj) = − pi pj  (i ≠ j)

Menunjukkan adanya hubungan negatif antar kategori.

---

## Bentuk Matriks Kovarian

Σ = P − p pᵀ

Dimana:
- P = diag(p1, p2, ..., pm)
- p = vektor probabilitas

Sifat penting:
- Matriks simetris
- Jumlah setiap baris dan kolom = 0

---

# Matriks Kovarian Sampel

Estimasi kovarian sampel:

Σ̂ = P̂ − p̂ p̂ᵀ

Dimana:
- P̂ = diag(p̂)
- p̂ = μ̂

Hasil ini sama dengan perhitungan kovarian standar menggunakan data yang sudah dicentering.

---

# Analisa Bivariat

Untuk dua atribut kategorikal X1 dan X2:

- Dataset berbentuk matriks n × 2
- Masing-masing atribut dimodelkan sebagai multivariate Bernoulli
- Probabilitas tiap kategori dihitung menggunakan pmf empiris

Tujuan:
Menganalisis hubungan antara dua atribut kategorikal menggunakan pendekatan probabilistik.
