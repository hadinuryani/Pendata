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

# Mengukur Jarak 

Mengukur jarak (dissimilarity) antar dua objek data dari dataset Titanic berdasarkan tipe datanya masing-masing.

```{code-cell}
:tags: [hide-input]
import pandas as pd
import numpy as np

df = pd.read_csv("../../assets/train.csv")
df.head(5)
```

---