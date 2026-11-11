# Analisis Sentimen Berbasis Aspek (ABSA) pada Komentar TikTok tentang Penutupan TPA Suwung dengan Metode IndoBERT

Repositori ini berisi implementasi lengkap penelitian skripsi **Aspect-Based Sentiment Analysis (ABSA)** menggunakan model **IndoBERT (`indobenchmark/indobert-base-p2`)** dengan skema **Joint Task Labeling (12 Kelas)** pada dataset komentar media sosial TikTok terkait kebijakan penutupan **Tempat Pembuangan Akhir (TPA) Suwung**, Denpasar, Bali.

## 📌 Ringkasan Proyek

Kebijakan penutupan TPA Suwung memicu beragam respons publik di media sosial, mulai dari dukungan demi kelestarian lingkungan hingga keluhan mendesak terkait kesiapan fasilitas pengolahan sampah dan retribusi banjar. Proyek ini mengembangkan alur pemrosesan NLP menyeluruh mulai dari prapemrosesan teks, normalisasi bahasa daerah (Bali), pelabelan aspek dan sentimen, hingga *fine-tuning* model *transformer* mutakhir.

### 🌟 Sorotan Utama & Kontribusi:

1. **Normalisasi Bahasa Daerah Bali (Leksikon Empiris)**:
   Mengatasi tantangan *code-mixing* bahasa daerah pada komentar TikTok warga Bali melalui kamus normalisasi khusus (200+ leksikon Bali: negasi seperti *sing, sg, ten*, kosakata lingkungan seperti *lulu, mis, ngutang*, aktivitas *ngukir, meli, mayah*, serta penanganan sufiks *-ne* →\\rightarrow→ *-nya*).

2. **Pemetaan 4 Kategori Aspek Utama**:

   * `Kebijakan Pemerintah`

   * `Infrastruktur Pengolahan`

   * `Dampak Lingkungan`

   * `Beban Masyarakat`

3. **Pencegahan Kebocoran Data (Zero Data Leakage)**:

   * Pembagian data 80:20 menggunakan `GroupShuffleSplit` berbasis ID Komentar asli untuk menjamin tidak ada tumpang tindih (*zero comment overlap*) antara data latih dan uji.

   * Kosakata (*vocabulary*) dan pembobotan TF-IDF dibentuk **hanya dari data latih**.

   * Penyeimbangan kelas (*class balancing*) diterapkan **eksklusif pada data latih**, menjaga distribusi data uji tetap alami.

4. **Joint Task Labeling (12 Kelas Numerik)**:
   Menggabungkan klasifikasi aspek dan polaritas sentimen (*Positif*, *Negatif*, *Netral*) ke dalam 12 kelas tunggal terpadu sesuai standar metodologi Bab III.

5. **Pelatihan IndoBERT 20 Epoch**:
   Dilengkapi *Best Model Checkpointing* berbasis *Validation Loss* untuk menghasilkan generalisasi optimal dan mencegah *overfitting*.

## 🏗️ Struktur Arsitektur & Pipeline

```
Dataset Mentah (1.202 Komentar TikTok)
               │
               ▼
   [Text Preprocessing & Data Cleaning]
   ├── Case Folding & URL / Mention / Hashtag Removal
   ├── Normalisasi Bahasa Daerah Bali (200+ Kosakata & Sufiks)
   ├── Normalisasi Slang & Singkatan Bahasa Indonesia
   └── Negation Preservation (tidak, bukan, jangan, belum, sing)
               │
               ▼
[Pembentukan Pasangan Komentar–Aspek (1.391 Pasangan)]
   ├── Ekstraksi Multi-Aspek (4 Kategori)
   └── Skoring Sentimen Kontekstual InSet Lexicon
               │
               ▼
    [Group Split 80:20 Bebas Tumpang Tindih]
   ├── Data Latih (80% / 1.116 Pasangan) ──> Penyeimbangan Kelas
   └── Data Uji (20% / 275 Pasangan)     ──> Distribusi Asli Unbiased
               │
               ▼
    [Pemodelan & Komparasi Algoritma]
   ├── Baseline Model : TF-IDF + Multinomial Naive Bayes
   └── Proposed Model : IndoBERT Base P2 (Joint Task 12 Kelas, 20 Epoch)
               │
               ▼
       [Evaluasi & Visualisasi]
   ├── Confusion Matrix, Precision, Recall, Macro F1-Score
   ├── Word Cloud 4 Kategori Aspek
   └── Dashboard Distribusi Opini & Uji Inferensi Komentar Baru


```

## 📊 Hasil Evaluasi & Komparasi Model

Evaluasi dilakukan pada dataset uji independen (*test set*) bebas kebocoran:

| Metrik Evaluasi | Baseline (Multinomial Naive Bayes) | Model Usulan (Fine-Tuned IndoBERT) | 
| ----- | ----- | ----- | 
| **Akurasi Sentimen (ABSA)** | \~70 - 75% | **> 85%** | 
| **Macro F1-Score** | \~65 - 70% | **> 80%** | 
| **Pemrosesan Konteks Negasi** | Terbatas pada N-gram | **Sangat Baik (Self-Attention)** | 
| **Representasi Semantik** | Bag of Words (TF-IDF) | **Kontekstual Mendalam (BERT)** | 

## 📁 Struktur Berkas

```
├── IndoBERT_ABSA_TPA_Suwung_T4_GPU.ipynb  # Notebook Google Colab lengkap (End-to-End)
├── IndoBERT_ABSA_TPA_Suwung_Revisi.py      # Skrip Python executable mandiri
├── data_suwung_setelah dipilah.xlsx         # Dataset komentar asli TikTok
├── dataset_pasangan_komentar_aspek.xlsx     # Dataset 1.391 pasangan komentar-aspek
├── data_latih_pasangan_80.xlsx              # Data latih hasil Group Split (1.116 baris)
├── data_uji_pasangan_20.xlsx                # Data uji hasil Group Split (275 baris)
└── README.md                                # Dokumentasi proyek & portofolio


```

## 🚀 Panduan Menjalankan di Google Colab

1. Buka notebook [Google Colab](https://colab.research.google.com/).

2. Pastikan file dataset `data_suwung_setelah dipilah.xlsx` berada di Google Drive folder `INDOBERT/` atau unggah langsung ke Colab.

3. Atur akselerator perangkat keras:

   * Pilih menu **Runtime** → **Change runtime type** → **T4 GPU** (atau CPU).

4. Jalankan seluruh *cell* secara berurutan (**Runtime** → **Run all**).

5. Hasil evaluasi model, matriks konfusi, grafik, serta *file* Excel akan otomatis tersimpan.

## 🛠️ Tumpukan Teknologi (Tech Stack)

* **Bahasa Pemrograman**: Python 3.8+

* **Deep Learning / NLP**: PyTorch, Hugging Face Transformers, IndoBERT (`indobenchmark/indobert-base-p2`), InSet Lexicon

* **Machine Learning**: Scikit-Learn, Imbalanced-Learn

* **Analisis & Manipulasi Data**: Pandas, NumPy, OpenPyXL

* **Visualisasi Data**: Matplotlib, Seaborn, WordCloud

* **Lingkungan Komputasi**: Google Colab (Tesla T4 GPU & High-RAM CPU)

## ✍️ Sitasi & Kontak

Proyek ini dikembangkan sebagai bagian dari penelitian skripsi mengenai analisis sentimen percakapan publik pada isu lingkungan dan kebijakan perkotaan di Indonesia.

Jika repositori ini bermanfaat untuk penelitian atau proyek Anda, jangan ragu untuk memberikan ⭐ **Star** dan menyertakan sitasi yang relevan!