## 📌 Ringkasan Proyek

Kebijakan penutupan TPA Suwung memicu beragam respons publik di media sosial, mulai dari dukungan demi kelestarian lingkungan hingga keluhan mengenai kesiapan fasilitas pengolahan sampah dan retribusi banjar. Proyek ini mengembangkan alur pemrosesan NLP menyeluruh mulai dari prapemrosesan teks, normalisasi bahasa daerah (Bali), pelabelan manual 3 anotator, hingga *fine-tuning* IndoBERT selama 20 epoch.

## 🏗️ Struktur Arsitektur & Pipeline

```text
Dataset Mentah (1.202 Komentar TikTok)
               │
               ▼
   [Text Preprocessing & Data Cleaning]
   ├── Case Folding & URL / Mention / Hashtag Removal
   ├── Normalisasi Bahasa Daerah Bali (200+ Kosakata & Sufiks)
   ├── Normalisasi Slang & Singkatan Bahasa Indonesia
   └── Negation Preservation (tidak, bukan, jangan, belum, tak)
               │
               ▼
[Pelabelan Manual & Uji Reliabilitas (1.202 Komentar)]
   ├── Pelabelan Independen 3 Anotator (Positif, Negatif, Netral)
   ├── Uji Fleiss' Kappa (κ = 0.7689 - Substantial Agreement)
   └── Penetapan Label Acuan Final (Majority Voting)
               │
               ▼
    [Stratified Split 80:20 Zero Overlap]
   ├── Data Latih (80% / 960 Komentar) ──> Penyeimbangan Kelas
   └── Data Uji (20% / 242 Komentar)   ──> Distribusi Asli Unbiased
               │
               ▼
    [Pemodelan & Komparasi]
   ├── Metode Utama     : IndoBERT Base P2 (3 Kelas, 20 Epoch)
   └── Model Pembanding : TF-IDF + Multinomial Naive Bayes (Baseline)
               │
               ▼
       [Evaluasi & Visualisasi]
   ├── Confusion Matrix, Accuracy, Precision, Recall, Macro F1-Score
   ├── Word Cloud 3 Kategori Sentimen (Positif, Negatif, Netral)
   └── Dashboard Distribusi Opini & Uji Inferensi Komentar Baru
```

## 🚀 Panduan Menjalankan di Google Colab

1. Buka notebook di [Google Colab](https://colab.research.google.com/).
2. Pastikan file dataset `data_suwung_setelah dipilah.xlsx` berada di Google Drive folder `INDOBERT/` atau unggah langsung ke Colab.
3. Atur akselerator perangkat keras: pilih **Runtime** → **Change runtime type** → **T4 GPU** (atau CPU).
4. Jalankan seluruh *cell* secara berurutan (**Runtime** → **Run all**).
5. Hasil evaluasi model, matriks konfusi, grafik, serta *file* Excel akan otomatis tersimpan.
