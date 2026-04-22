# Analisa Data Kesuburan Tanah — K-Nearest Neighbors (KNN)

> **Dataset:** `dataset_kesuburan_tanah_missing.xlsx` | 2.000 sampel | 10 fitur | 2 kelas  
> **Tugas:** KNN + Pemrosesan Data + Evaluasi (Accuracy, Precision, Recall, F1-Score)

---

## 📦 Cell 1 — Import Library

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns

from sklearn.model_selection import train_test_split, cross_val_score
from sklearn.preprocessing import LabelEncoder, StandardScaler
from sklearn.impute import SimpleImputer
from sklearn.neighbors import KNeighborsClassifier
from sklearn.metrics import (
    accuracy_score, precision_score,
    recall_score, f1_score,
    classification_report, confusion_matrix,
    ConfusionMatrixDisplay
)

import warnings
warnings.filterwarnings('ignore')

print("✅ Library berhasil diimport!")
```

---

## 📂 Cell 2 — Load Dataset

```python
df = pd.read_csv('dataset_kesuburan_tanah_missing_xlsx_-_Dataset.csv')

print("Shape dataset :", df.shape)
print("Kolom         :", list(df.columns))
print()
df.head(10)
```

---

## 🔍 Cell 3 — Eksplorasi Data (EDA)

```python
# ── Informasi tipe data ──────────────────────────────────────────
print("Info Dataset:")
print(df.info())
```

```python
# ── Statistik deskriptif ─────────────────────────────────────────
print("Statistik Deskriptif:")
df.describe().round(3)
```

```python
# ── Distribusi label target ──────────────────────────────────────
print("Distribusi Label:")
print(df['Label'].value_counts())
print()

fig, axes = plt.subplots(1, 2, figsize=(12, 4))

# Bar chart
df['Label'].value_counts().plot(
    kind='bar', ax=axes[0],
    color=['steelblue', 'tomato'], edgecolor='black'
)
axes[0].set_title('Distribusi Label Kesuburan Tanah')
axes[0].set_xlabel('Label')
axes[0].set_ylabel('Jumlah')
axes[0].tick_params(axis='x', rotation=0)

# Pie chart
df['Label'].value_counts().plot(
    kind='pie', ax=axes[1],
    colors=['steelblue', 'tomato'],
    autopct='%1.1f%%', startangle=90
)
axes[1].set_title('Proporsi Label')
axes[1].set_ylabel('')

plt.tight_layout()
plt.show()
```

```python
# ── Distribusi Tekstur Tanah ─────────────────────────────────────
print("Distribusi Tekstur Tanah:")
print(df['Tekstur Tanah'].value_counts())

plt.figure(figsize=(9, 4))
df['Tekstur Tanah'].value_counts().plot(
    kind='bar', color='mediumseagreen', edgecolor='black'
)
plt.title('Distribusi Tekstur Tanah')
plt.xlabel('Tekstur')
plt.ylabel('Jumlah')
plt.xticks(rotation=30, ha='right')
plt.tight_layout()
plt.show()
```

```python
# ── Boxplot fitur numerik per label ─────────────────────────────
fitur_numerik = [
    'pH Tanah', 'N Total (%)', 'P Tersedia (ppm)',
    'K Tersedia (meq/100g)', 'C Organik (%)', 'KTK (meq/100g)',
    'Kejenuhan Basa (%)', 'Kadar Air (%)', 'Bulk Density (g/cm³)'
]

fig, axes = plt.subplots(3, 3, figsize=(16, 12))
axes = axes.flatten()

for i, col in enumerate(fitur_numerik):
    data_subur     = df[df['Label'] == 'Subur'][col].dropna()
    data_tdk_subur = df[df['Label'] == 'Tidak Subur'][col].dropna()
    axes[i].boxplot([data_subur, data_tdk_subur],
                    labels=['Subur', 'Tidak Subur'],
                    patch_artist=True,
                    boxprops=dict(facecolor='lightblue'))
    axes[i].set_title(col, fontsize=10)
    axes[i].grid(True, alpha=0.3)

plt.suptitle('Distribusi Fitur Numerik per Label', fontsize=14, y=1.01)
plt.tight_layout()
plt.show()
```

---

## ❓ Cell 4 — Analisis Missing Values

```python
# ── Hitung missing values ────────────────────────────────────────
missing_count = df.isnull().sum()
missing_pct   = (missing_count / len(df) * 100).round(2)

missing_df = pd.DataFrame({
    'Jumlah Missing': missing_count,
    'Persentase (%)': missing_pct
}).query('`Jumlah Missing` > 0').sort_values('Jumlah Missing', ascending=False)

print("Rekap Missing Values:")
print(missing_df)
print(f"\nTotal sel kosong : {missing_count.sum()} dari {df.shape[0] * (df.shape[1]-2)} nilai")
```

```python
# ── Visualisasi missing values ───────────────────────────────────
fig, ax = plt.subplots(figsize=(10, 5))
bars = ax.barh(missing_df.index, missing_df['Persentase (%)'],
               color='salmon', edgecolor='black')

for bar, val in zip(bars, missing_df['Persentase (%)']):
    ax.text(bar.get_width() + 0.1, bar.get_y() + bar.get_height()/2,
            f'{val}%', va='center', fontsize=10)

ax.set_xlabel('Persentase Missing (%)')
ax.set_title('Missing Values per Fitur', fontsize=14)
ax.grid(True, axis='x', alpha=0.3)
plt.tight_layout()
plt.show()
```

---

## 🧹 Cell 5 — Pemrosesan Data

### 5.1 — Hapus Kolom ID

```python
df_clean = df.drop(columns=['ID']).copy()
print("Kolom ID dihapus. Shape sekarang:", df_clean.shape)
```

### 5.2 — Imputasi Missing Values

```python
# Fitur numerik → isi dengan MEDIAN (robust terhadap outlier)
imputer_num = SimpleImputer(strategy='median')
df_clean[fitur_numerik] = imputer_num.fit_transform(df_clean[fitur_numerik])

# Fitur kategorikal → isi dengan MODUS (nilai terbanyak)
imputer_cat = SimpleImputer(strategy='most_frequent')
df_clean[['Tekstur Tanah']] = imputer_cat.fit_transform(df_clean[['Tekstur Tanah']])

print("✅ Imputasi selesai.")
print("Sisa missing values:", df_clean.isnull().sum().sum())
```

### 5.3 — Encoding Fitur Kategorikal

```python
le_tekstur = LabelEncoder()
df_clean['Tekstur Tanah'] = le_tekstur.fit_transform(df_clean['Tekstur Tanah'])

print("Mapping Tekstur Tanah:")
for kelas, kode in zip(le_tekstur.classes_, le_tekstur.transform(le_tekstur.classes_)):
    print(f"  {kelas:20s} → {kode}")
```

```python
# Encode label target
le_label = LabelEncoder()
df_clean['Label_enc'] = le_label.fit_transform(df_clean['Label'])

print("\nMapping Label:")
for kelas, kode in zip(le_label.classes_, le_label.transform(le_label.classes_)):
    print(f"  {kelas:15s} → {kode}")
```

### 5.4 — Normalisasi Fitur (StandardScaler)

> ⚠️ **Wajib untuk KNN!** KNN menghitung jarak antar titik — fitur dengan skala besar akan mendominasi jika tidak dinormalisasi.

```python
X = df_clean[fitur_numerik + ['Tekstur Tanah']]
y = df_clean['Label_enc']

scaler = StandardScaler()
X_scaled = scaler.fit_transform(X)
X_scaled = pd.DataFrame(X_scaled, columns=X.columns)

print("✅ Normalisasi selesai.")
print("Contoh data setelah scaling (5 baris pertama):")
print(X_scaled.head().round(3))
```

---

## ✂️ Cell 6 — Split Data Train & Test

```python
X_train, X_test, y_train, y_test = train_test_split(
    X_scaled, y,
    test_size=0.2,       # 80% train, 20% test
    random_state=42,
    stratify=y           # pastikan proporsi label seimbang
)

print(f"Total data   : {len(X_scaled)}")
print(f"Data Train   : {len(X_train)} ({len(X_train)/len(X_scaled)*100:.0f}%)")
print(f"Data Test    : {len(X_test)}  ({len(X_test)/len(X_scaled)*100:.0f}%)")
print()
print("Distribusi label train:", pd.Series(y_train).value_counts().to_dict())
print("Distribusi label test :", pd.Series(y_test).value_counts().to_dict())
```

---

## 🔎 Cell 7 — Pemilihan K Optimal

```python
# ── Uji nilai K dari 1 sampai 20 ────────────────────────────────
k_range   = range(1, 21)
acc_list  = []
cv_list   = []

for k in k_range:
    knn = KNeighborsClassifier(n_neighbors=k, metric='euclidean')
    knn.fit(X_train, y_train)
    acc = accuracy_score(y_test, knn.predict(X_test))
    acc_list.append(acc)

    cv_score = cross_val_score(knn, X_scaled, y, cv=5, scoring='accuracy').mean()
    cv_list.append(cv_score)

best_k    = k_range[np.argmax(cv_list)]
best_cv   = max(cv_list)

print(f"K terbaik (berdasarkan CV): K = {best_k}")
print(f"CV Accuracy terbaik       : {best_cv:.4f} ({best_cv*100:.2f}%)")
```

```python
# ── Visualisasi pemilihan K ──────────────────────────────────────
plt.figure(figsize=(12, 5))
plt.plot(k_range, [a*100 for a in acc_list], 'o-',
         color='steelblue', linewidth=2, label='Test Accuracy')
plt.plot(k_range, [a*100 for a in cv_list], 's--',
         color='darkorange', linewidth=2, label='CV Accuracy (5-fold)')
plt.axvline(x=best_k, color='red', linestyle=':', linewidth=2,
            label=f'Best K = {best_k}')

plt.title('Pemilihan Nilai K Optimal pada KNN', fontsize=14)
plt.xlabel('Nilai K (Jumlah Tetangga)')
plt.ylabel('Accuracy (%)')
plt.xticks(k_range)
plt.legend()
plt.grid(True, alpha=0.3)
plt.tight_layout()
plt.show()
```

---

## 🤖 Cell 8 — Training Model KNN

```python
knn_model = KNeighborsClassifier(
    n_neighbors = best_k,
    metric      = 'euclidean',  # jarak Euclidean
    weights     = 'uniform'     # semua tetangga berbobot sama
)

knn_model.fit(X_train, y_train)
y_pred = knn_model.predict(X_test)

print(f"✅ Model KNN (K={best_k}) berhasil dilatih!")
```

---

## 📊 Cell 9 — Metrik Evaluasi

```python
# ── Hitung semua metrik ──────────────────────────────────────────
acc       = accuracy_score(y_test, y_pred)
precision = precision_score(y_test, y_pred)
recall    = recall_score(y_test, y_pred)
f1        = f1_score(y_test, y_pred)

print("=" * 50)
print(f"  HASIL EVALUASI KNN (K = {best_k})")
print("=" * 50)
print(f"  Accuracy  : {acc:.4f}  ({acc*100:.2f}%)")
print(f"  Precision : {precision:.4f}  ({precision*100:.2f}%)")
print(f"  Recall    : {recall:.4f}  ({recall*100:.2f}%)")
print(f"  F1-Score  : {f1:.4f}  ({f1*100:.2f}%)")
print("=" * 50)
```

```python
# ── Classification Report lengkap ───────────────────────────────
print("Classification Report Lengkap:")
print(classification_report(
    y_test, y_pred,
    target_names=le_label.classes_
))
```

```python
# ── Penjelasan tiap metrik ───────────────────────────────────────
penjelasan = pd.DataFrame({
    'Metrik'    : ['Accuracy', 'Precision', 'Recall', 'F1-Score'],
    'Nilai'     : [f'{acc:.4f}', f'{precision:.4f}', f'{recall:.4f}', f'{f1:.4f}'],
    'Persentase': [f'{acc*100:.2f}%', f'{precision*100:.2f}%',
                   f'{recall*100:.2f}%', f'{f1*100:.2f}%'],
    'Keterangan': [
        'Persentase prediksi benar dari total data',
        'Ketepatan prediksi kelas positif (Subur)',
        'Kemampuan mendeteksi seluruh kelas positif (Subur)',
        'Harmonic mean antara Precision dan Recall'
    ]
})
print(penjelasan.to_string(index=False))
```

---

## 🔲 Cell 10 — Confusion Matrix

```python
cm   = confusion_matrix(y_test, y_pred)
tn, fp, fn, tp = cm.ravel()

print(f"True Negative  (TN) — Tidak Subur diprediksi Tidak Subur : {tn}")
print(f"False Positive (FP) — Tidak Subur diprediksi Subur        : {fp}")
print(f"False Negative (FN) — Subur diprediksi Tidak Subur        : {fn}")
print(f"True Positive  (TP) — Subur diprediksi Subur              : {tp}")
```

```python
fig, axes = plt.subplots(1, 2, figsize=(14, 5))

# ── Confusion Matrix plot ────────────────────────────────────────
disp = ConfusionMatrixDisplay(
    confusion_matrix=cm,
    display_labels=le_label.classes_
)
disp.plot(ax=axes[0], colorbar=False, cmap='Blues')
axes[0].set_title(f'Confusion Matrix\nKNN (K={best_k})', fontsize=13)

# ── Bar chart metrik ─────────────────────────────────────────────
metrik_names  = ['Accuracy', 'Precision', 'Recall', 'F1-Score']
metrik_values = [acc, precision, recall, f1]
colors        = ['#2196F3', '#4CAF50', '#FF9800', '#9C27B0']

bars = axes[1].bar(metrik_names, [v*100 for v in metrik_values],
                   color=colors, edgecolor='black', width=0.5)
for bar, val in zip(bars, metrik_values):
    axes[1].text(bar.get_x() + bar.get_width()/2,
                 bar.get_height() + 0.5,
                 f'{val*100:.2f}%', ha='center', fontsize=12, fontweight='bold')

axes[1].set_ylim(0, 115)
axes[1].set_ylabel('Nilai (%)', fontsize=12)
axes[1].set_title(f'Ringkasan Metrik Evaluasi\nKNN (K={best_k})', fontsize=13)
axes[1].grid(True, axis='y', alpha=0.3)

plt.tight_layout()
plt.show()
```

---

## 🔁 Cell 11 — Cross-Validation (Validasi Lebih Robust)

```python
cv_scores = cross_val_score(knn_model, X_scaled, y, cv=5, scoring='accuracy')

print("5-Fold Cross-Validation:")
for i, score in enumerate(cv_scores, 1):
    print(f"  Fold {i}: {score:.4f} ({score*100:.2f}%)")
print(f"\nMean  : {cv_scores.mean():.4f} ({cv_scores.mean()*100:.2f}%)")
print(f"Std   : {cv_scores.std():.4f}")
```

```python
plt.figure(figsize=(8, 4))
plt.bar([f'Fold {i}' for i in range(1, 6)], cv_scores * 100,
        color='steelblue', edgecolor='black')
plt.axhline(y=cv_scores.mean()*100, color='red', linestyle='--',
            label=f'Mean = {cv_scores.mean()*100:.2f}%')
plt.ylim(0, 110)
plt.title('Hasil 5-Fold Cross-Validation KNN', fontsize=13)
plt.ylabel('Accuracy (%)')
plt.legend()
plt.grid(True, axis='y', alpha=0.3)
plt.tight_layout()
plt.show()
```

---

## 🎯 Cell 12 — Prediksi Sampel Baru

```python
# ── Contoh prediksi 1 sampel tanah baru ─────────────────────────
# Tekstur Tanah encoding:
print("Kode Tekstur Tanah:")
for kelas, kode in zip(le_tekstur.classes_, range(len(le_tekstur.classes_))):
    print(f"  {kelas:20s} → {kode}")
```

```python
# Isi nilai sesuai tanah yang ingin diprediksi
sampel = pd.DataFrame([{
    'pH Tanah'              : 6.5,
    'N Total (%)'           : 0.35,
    'P Tersedia (ppm)'      : 40.0,
    'K Tersedia (meq/100g)' : 0.55,
    'C Organik (%)'         : 3.2,
    'KTK (meq/100g)'        : 30.0,
    'Kejenuhan Basa (%)'    : 75.0,
    'Kadar Air (%)'         : 35.0,
    'Bulk Density (g/cm³)'  : 1.1,
    'Tekstur Tanah'         : 1      # contoh: Lempung
}])

# Normalisasi sampel menggunakan scaler yang sama
sampel_scaled = scaler.transform(sampel)

# Prediksi
prediksi = knn_model.predict(sampel_scaled)
proba    = knn_model.predict_proba(sampel_scaled)

label_hasil = le_label.inverse_transform(prediksi)[0]
print(f"\nHasil Prediksi : {label_hasil}")
print(f"Probabilitas   : Subur={proba[0][1]:.2f} | Tidak Subur={proba[0][0]:.2f}")
```

---

## 📝 Cell 13 — Kesimpulan

```python
print("=" * 60)
print("               K E S I M P U L A N")
print("=" * 60)
print(f"""
Dataset   : Kesuburan Tanah (2.000 data, 10 fitur)
Target    : Subur / Tidak Subur (seimbang 50:50)

Pemrosesan Data:
  → Imputasi numerik  : Median (robust terhadap outlier)
  → Imputasi kategori : Modus (nilai terbanyak)
  → Encoding          : LabelEncoder (Tekstur + Label)
  → Normalisasi       : StandardScaler (wajib untuk KNN)
  → Split data        : 80% train / 20% test (stratified)

Model     : K-Nearest Neighbors (K = {best_k})
Metrik    : Euclidean Distance | Weights = Uniform

Hasil Evaluasi:
  ✅ Accuracy  : {acc*100:.2f}%
  ✅ Precision : {precision*100:.2f}%
  ✅ Recall    : {recall*100:.2f}%
  ✅ F1-Score  : {f1*100:.2f}%
  ✅ CV (5-fold): {cv_scores.mean()*100:.2f}% ± {cv_scores.std()*100:.2f}%
""")
print("=" * 60)
```

---

## 📋 Referensi Nilai Fitur (dari Deskripsi Dataset)

| Fitur | Nilai Subur | Nilai Tidak Subur |
|---|---|---|
| pH Tanah | 6,0 – 7,5 | < 5,5 atau > 7,5 |
| N Total (%) | 0,21 – 0,50% | 0,01 – 0,20% |
| P Tersedia (ppm) | 15 – 60 ppm | 1 – 14 ppm |
| K Tersedia (meq/100g) | 0,30 – 0,80 | 0,05 – 0,29 |
| C Organik (%) | 2,0 – 5,0% | 0,2 – 1,9% |
| KTK (meq/100g) | 20 – 45 | 5 – 19 |
| Kejenuhan Basa (%) | 60 – 100% | 10 – 59% |
| Tekstur Tanah | Lempung, Lempung Berpasir, Lempung Berliat | Pasir, Liat, Debu |
| Kadar Air (%) | 25 – 45% | < 20% atau > 55% |
| Bulk Density (g/cm³) | 0,9 – 1,2 | 1,4 – 1,9 |
