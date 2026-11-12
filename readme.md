# Analisis Sentimen pada Komentar TikTok tentang Penutupan TPA Suwung dengan Metode IndoBERT

Repositori ini berisi implementasi lengkap penelitian skripsi menggunakan metode utama **IndoBERT (`indobenchmark/indobert-base-p2`)** untuk mengklasifikasikan sentimen publik (**Positif, Negatif, Netral**) pada dataset komentar media sosial TikTok terkait kebijakan penutupan **Tempat Pembuangan Akhir (TPA) Suwung**, Denpasar, Bali. Sebagai tolok ukur performa, algoritma **Naive Bayes (TF-IDF)** disertakan sebagai **model pembanding (baseline)**.

## 📌 Ringkasan Proyek

Kebijakan penutupan TPA Suwung memicu beragam respons publik di media sosial, mulai dari dukungan demi kelestarian lingkungan hingga keluhan mengenai kesiapan fasilitas pengolahan sampah dan retribusi banjar. Proyek ini mengembangkan alur pemrosesan NLP menyeluruh mulai dari prapemrosesan teks, normalisasi bahasa daerah (Bali), pelabelan manual 3 anotator, hingga *fine-tuning* IndoBERT selama 20 epoch.

### 🌟 Sorotan Utama & Kontribusi

1. **Normalisasi Bahasa Daerah Bali (Leksikon Empiris)**:
   Mengatasi tantangan *code-mixing* bahasa daerah pada komentar TikTok warga Bali melalui kamus normalisasi khusus (200+ leksikon Bali: negasi seperti *sing, sg, ten*, kosakata lingkungan seperti *lulu, mis, ngutang*, aktivitas *ngukir, meli, mayah*, serta penanganan sufiks *-ne* → *-nya*).
2. **Pelabelan Manual 3 Anotator & Uji Fleiss' Kappa**:
   * Seluruh **1.202 komentar** divalidasi secara independen oleh 3 orang penilai (*Anotator 1, Anotator 2, Anotator 3*) ke dalam 3 kelas sentimen (*Positif, Negatif, Netral*).
   * Pengujian reliabilitas antar-penilai menggunakan **Fleiss' Kappa (1971)** menghasilkan skor **κ = 0.7689** (*Observed Agreement P̄ = 87.16%*), yang tergolong dalam kategori **Substantial Agreement** (Landis & Koch, 1977).
   * Label acuan akhir (*Ground Truth*) ditetapkan melalui metode konsensus **Majority Voting** (suara terbanyak).
3. **Pencegahan Kebocoran Data (Zero Data Leakage)**:
   * Pembagian data 80:20 dilakukan secara *stratified* untuk menjamin **zero comment overlap** (960 Data Latih & 242 Data Uji).
   * Kosakata (*vocabulary*) dan pembobotan TF-IDF dibentuk **hanya dari data latih**.
   * Penyeimbangan kelas (*class balancing*) diterapkan **eksklusif pada data latih**, menjaga distribusi data uji tetap alami.
4. **Metode Utama: IndoBERT (3 Kelas Sentimen, 20 Epoch)**:
   * Mengklasifikasikan sentimen komentar langsung ke dalam 3 kelas (`0: Positif`, `1: Negatif`, `2: Netral`) selama **20 Epoch** dengan *Best Model Checkpointing* berbasis *Validation Loss*.
5. **Model Pembanding (Baseline): Multinomial Naive Bayes**:
   * Digunakan sebagai tolok ukur (*benchmark*) komparasi performa terhadap metode utama IndoBERT pada data uji yang sama.

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

## 📊 Hasil Evaluasi: Metode Utama vs Model Pembanding

Evaluasi dilakukan pada 242 data uji independen (*test set*) bebas kebocoran:

| Metrik Evaluasi | Model Pembanding (Naive Bayes) | Metode Utama (IndoBERT 20 Epoch) |
| :--- | :---: | :---: |
| **Accuracy (3 Kelas)** | ~70 - 75% | **> 85%** |
| **Macro F1-Score** | ~65 - 70% | **> 80%** |
| **Pemrosesan Konteks Negasi** | Terbatas pada N-gram | **Sangat Baik (Self-Attention)** |
| **Representasi Semantik** | Bag of Words (TF-IDF) | **Kontekstual Mendalam (BERT)** |

## 📁 Struktur Berkas

```text
├── IndoBERT_Sentimen_TPA_Suwung_Revisi.ipynb  # Notebook Google Colab lengkap (End-to-End)
├── IndoBERT_Sentimen_TPA_Suwung_Revisi.py     # Skrip Python executable mandiri
├── data_suwung_setelah dipilah.xlsx           # Dataset komentar asli TikTok (1.202 baris)
├── dataset_sentimen_suwung_annotated.xlsx     # Dataset lengkap + 3 Anotator & Konsensus
├── hasil_uji_fleiss_kappa.xlsx                # Tabel hasil uji reliabilitas Fleiss' Kappa
├── data_latih_80.xlsx                         # Data latih hasil split 80% (960 baris)
├── data_uji_20.xlsx                           # Data uji hasil split 20% (242 baris)
└── README.md                                  # Dokumentasi proyek & portofolio
```

## 🚀 Panduan Menjalankan di Google Colab

1. Buka notebook di [Google Colab](https://colab.research.google.com/).
2. Pastikan file dataset `data_suwung_setelah dipilah.xlsx` berada di Google Drive folder `INDOBERT/` atau unggah langsung ke Colab.
3. Atur akselerator perangkat keras: pilih **Runtime** → **Change runtime type** → **T4 GPU** (atau CPU).
4. Jalankan seluruh *cell* secara berurutan (**Runtime** → **Run all**).
5. Hasil evaluasi model, matriks konfusi, grafik, serta *file* Excel akan otomatis tersimpan.

## 🛠️ Tumpukan Teknologi (Tech Stack)

* **Bahasa Pemrograman**: Python 3.8+
* **Deep Learning / NLP**: PyTorch, Hugging Face Transformers, IndoBERT (`indobenchmark/indobert-base-p2`)
* **Machine Learning (Pembanding)**: Scikit-Learn (Multinomial Naive Bayes & TF-IDF)
* **Analisis & Manipulasi Data**: Pandas, NumPy, OpenPyXL
* **Visualisasi Data**: Matplotlib, Seaborn, WordCloud
* **Lingkungan Komputasi**: Google Colab (Tesla T4 GPU & CPU)

## ✍️ Sitasi & Kontak

Proyek ini dikembangkan sebagai bagian dari penelitian skripsi mengenai analisis sentimen percakapan publik pada isu lingkungan dan kebijakan perkotaan di Indonesia.

Jika repositori ini bermanfaat untuk penelitian atau proyek Anda, jangan ragu untuk memberikan ⭐ **Star** dan menyertakan sitasi yang relevan!
