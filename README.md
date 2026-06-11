# Analisa Pengaruh Ketersediaan Sanitasi Air Bersih terhadap Kasus DBD di Indonesia

> Proyek Data Engineering — Kelompok 8

---

## Kontributor

| Nama Lengkap | NIM | Peran |
|---|---|---|
| Arie Dwi Sulistyo | 244311004 | Data Engineer |
| Dila Alif Regita | 244311010 | Data Analyst |
| Suqya Rohmatin | 244311028 | Data Engineer |
| M. Rofiqul Anam | 244311019 | Project Manager |

---

## Deskripsi Proyek

Proyek ini dikembangkan untuk menganalisis pengaruh ketersediaan sanitasi air bersih (akses air minum layak) terhadap jumlah kasus Demam Berdarah Dengue (DBD) di Indonesia. Kami membangun *pipeline* data terintegrasi yang mengotomatisasi ekstraksi dataset dari BPS dan laporan historis Kemenkes, melakukan pembersihan data (*preprocessing*), menyimpannya ke dalam sistem *cloud database*, hingga memodelkannya. Tujuannya adalah untuk menemukan wawasan berbasis data (*data-driven*) guna mendukung kebijakan intervensi penanganan lingkungan yang lebih tepat sasaran.

---

## Tujuan & Manfaat

**Tujuan:**
Membangun infrastruktur *data pipeline* (ETL) yang stabil untuk mengekstrak, membersihkan, dan memusatkan penyimpanan data historis ketersediaan sanitasi air bersih serta kasus DBD dari 38 Provinsi di Indonesia.

**Manfaat:**
Menyediakan basis data terpusat yang terstruktur bagi analis kesehatan maupun pemangku kebijakan. Visualisasi yang dihasilkan akan mempermudah pemantauan tren persebaran penyakit serta menyoroti provinsi mana saja yang masuk ke dalam prioritas darurat (Zona Merah) perbaikan sanitasi.

---

## Serving Analysis

Data yang telah tersimpan dan dibersihkan di *database* MySQL akan disajikan melalui visualisasi interaktif menggunakan **Google Data Studio (Looker Studio)**. Melalui *dashboard* ini, pengguna dapat melihat analisis tren tahunan, pemetaan spasial, perbandingan rasio akses air bersih terhadap lonjakan kasus DBD antarprovinsi, serta agregasi data statistik secara visual dan *real-time*.

---

## Serving Machine Learning

Tahap penyajian model ini memanfaatkan data yang telah diproses untuk keperluan *Machine Learning* tanpa pengawasan (*Unsupervised Learning*). Kami memprediksi dan memetakan tingkat risiko lonjakan kasus DBD di suatu wilayah berdasarkan kualitas sanitasinya menggunakan algoritma klusterisasi, sehingga mendukung pengambilan keputusan pencegahan penyakit yang bersifat prediktif.

---

## Pipeline Arsitektur ETL

### 1. Extract (Pengambilan Data)

| Dataset | Sumber | Metode |
|---|---|---|
| Data Air Layak | [BPS - Persentase Rumah Tangga](https://www.bps.go.id/id/statistics-table/2/ODU0IzI=/persentase-rumah-tangga-yang-memiliki-akses-terhadap-sumber-air-minum-layak-menurut-provinsi-dan-klasifikasi-desa--persen-.html) | Unduh CSV / Web Scraping |
| Data Kasus DBD | [KEMENKES](https://www.kemkes.go.id/id/category/profil-kesehatan) | Digitalisasi Manual (Data Entry) ke CSV |

**Catatan Khusus Ekstraksi:** Data kasus DBD diekstrak dari laporan tahunan berformat gambar/citra PDF. Oleh karena itu, tim kami melakukan pemrosesan digitalisasi manual (data entry) menjadi format mentah `.csv` sebelum dimasukkan ke dalam pipeline ETL.

---

### 2. Transform (Pembersihan & Transformasi)

**Pembersihan:**
- **Menghapus data rusak:** Membuang baris data yang kosong (missing value) ataupun data ganda.
- **Menyeragamkan format teks:** Mengubah nama provinsi menjadi huruf kapital dan memperbaiki singkatan (contoh: "KEP. RIAU" menjadi "KEPULAUAN RIAU") untuk akurasi penggabungan data.
- **Membuang data tidak relevan:** Menghapus baris agregasi nasional ("INDONESIA") agar model fokus pada data granular per provinsi.
- **Memperbaiki tipe data:** Memastikan kolom metrik (seperti persentase air bersih dan jumlah kasus) terbaca dengan benar sebagai tipe data numerik.

**Transformasi:**
- **Menambahkan label waktu:** Membuat kolom baru berisi keterangan tahun (2021-2024) untuk analisis time-series.
- **Menggabungkan data (*Merging*):** Menyatukan dataset DBD dan Air Bersih menjadi satu dataframe utuh berdasarkan primary key gabungan (`Provinsi` dan `Tahun`).
- **Agregasi (*Grouping*):** Menghitung nilai rata-rata dari 4 tahun terakhir untuk menstabilkan titik data sebelum masuk ke model Machine Learning.

---

### 3. Load (Penyimpanan ke Database)

- **Target:** Sebuah tabel baru bernama `dbd_airminum` di dalam *database* **MySQL** pada *cloud server* **Aiven**.
- **Metode:**
  - Fungsi `to_sql()` dari Pandas digunakan untuk menulis data secara otomatis ke tabel tujuan.
  - Konfigurasi koneksi diatur menggunakan `engine` dari library **SQLAlchemy** yang disambungkan melalui driver `pymysql`.
  - Verifikasi integritas data dilakukan dengan menarik dan mencetak 5 baris pertama dari database menggunakan perintah `pd.read_sql()`.

---

## Arsitektur / Workflow ETL

- **Alur Modular:** Seluruh proses ETL dibungkus dalam fungsi utama transformasi() yang secara otomatis menjalankan tahapan pembacaan, pembersihan, penanganan nilai kosong, hingga penggabungan data.
- **Eksekusi Sekuensial:** Kode disusun secara berurutan dan terstruktur agar alur datanya mudah diikuti, direproduksi, dan dievaluasi langsung di dalam notebook Google Colab.

---

## Teknologi yang Digunakan

- **Bahasa Pemrograman:** Python 3.x
- **Environment:** Google Colab
- **Data Wrangling:** Pandas, NumPy
- **Database & Konektor:** MySQL (Aiven Cloud), SQLAlchemy, PyMySQL
- **Machine Learning:** Scikit-learn, StandardScaler
- **Visualisasi:** Matplotlib, Seaborn, Google Data Studio (Looker)

---

## Kode Program & Tautan Proyek

**Struktur Kode:**
Terdapat 2 modul utama dalam bentuk Jupyter Notebook:
1. `etl_pipeline.ipynb`: Menangani proses otomatisasi ETL dan injeksi ke MySQL.
2. `MachineLearning_kel8.ipynb`: Menangani standarisasi data, pencarian siku optimal, dan klusterisasi.

**Model Machine Learning:**
Algoritma yang digunakan adalah **K-Means Clustering**. Untuk menghindari bias dalam menentukan jumlah zona, kami menerapkan **Metode Elbow** (menganalisis visualisasi penurunan metrik WCSS dari $K=1$ hingga $K=10$). Ditemukan bahwa kurva melandai secara optimal di angka **$K=3$**, sehingga provinsi di Indonesia dibagi menjadi 3 kategori zona kerawanan.

**Tautan Penting:**
- **ETL Pipeline:** https://github.com/ArDS1401/sanitasi-terhadap-kasus-dbd/blob/main/Pipeline%20(1).ipynb
- **Machine Learning:** (https://github.com/ArDS1401/sanitasi-terhadap-kasus-dbd/blob/main/MachineLearning_kel8.ipynb)
- **Looker Dashboard:** (https://datastudio.google.com/reporting/ee37f130-9199-4607-a6c7-e2c58c5f83b4)

---
