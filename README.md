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

Proyek ini dikembangkan untuk menganalisis pengaruh ketersediaan sanitasi air bersih terhadap jumlah kasus Demam Berdarah Dengue (DBD) di Indonesia dengan menggunakan pengumpulan dataset publik dari BPS dan Kemenkes. Kemudian dilakukan pembersihan dan pra-pemrosesan data (data preprocessing), setelah itu data diolah dan dimodelkan. Dengan tujuan untuk mengetahui hubungan serta seberapa besar dampak kualitas sanitasi air bersih terhadap lonjakan kasus penyakit DBD.

---

## Tujuan & Manfaat

**Tujuan:**
Membangun data pipeline yang mengotomatisasi proses ekstraksi, pembersihan (transformasi), dan pemusatan penyimpanan (load) dari berbagai sumber data publik untuk mengintegrasikan dataset ketersediaan sanitasi air bersih dan jumlah kasus DBD di Indonesia.

**Manfaat:**
Proyek ini bermanfaat untuk menyediakan infrastruktur data terpusat yang mengintegrasikan informasi mengenai kondisi sanitasi air bersih dan tingkat persebaran kasus DBD di berbagai wilayah Indonesia secara otomatis. Ketersediaan basis data yang bersih dan siap pakai melalui pipeline ETL ini akan mempermudah para analis kesehatan maupun pemangku kebijakan dalam menarik wawasan secara cepat guna mendukung intervensi penanganan lingkungan yang lebih tepat sasaran.

---

## Serving Analis
Analisis pada proyek ini terdiri dari beberapa tahapan utama, yaitu pengambilan data dari sumber resmi seperti BPS dan Kementerian Kesehatan (extract), pembersihan serta penggabungan data sanitasi air bersih dan jumlah kasus DBD berdasarkan Provinsi dan tahun (transform), serta penyimpanan hasil ke dalam database PostgreSQL (load). Data yang telah tersimpan kemudian disajikan melalui visualisasi interaktif menggunakan tools seperti Streamlit untuk analisis tren, pemetaan spasial, dan korelasi antarvariabel. Selain itu, data juga dapat dimanfaatkan untuk keperluan machine learning seperti prediksi potensi risiko lonjakan kasus DBD di suatu daerah berdasarkan kualitas sanitasinya, sehingga mendukung pengambilan keputusan prediktif berbasis data.

---
## Serving Machine Learning
Serving Machine adalah tahap penyajian data dan model yang telah diproses agar dapat digunakan oleh pengguna akhir. Ini mencakup penyajian visualisasi interaktif melalui Streamlit, serta pemanfaatan data tersebut untuk keperluan machine learning seperti memprediksi tingkat risiko atau potensi lonjakan kasus DBD di suatu wilayah berdasarkan kualitas sanitasinya, sehingga mendukung pengambilan keputusan pencegahan penyakit yang berbasis data.

---

## Pipeline 

### 1. Extract — Pengambilan Data

| Dataset | Sumber | Metode |
|---|---|---|
| Data Kasus DBD | [SIPSN KLHK](https://sipsn.menlhk.go.id/sipsn/public/data/timbulan) | JSON API |
| Data | [BPS](https://www.bps.go.id/id/statistics-table/2/OTU5IzI=/banyaknya-desa-kelurahan-menurut-jenis-pencemaran-lingkungan-hidup.html) | Scraping HTML (Selenium) |
| Jumlah Penduduk per Provinsi | [BPS Sulut](https://sulut.bps.go.id/id/statistics-table/2/OTU4IzI=/jumlah-penduduk-menurut-provinsi-di-indonesia.html) | Scraping HTML (Selenium) |

### 2. Transform — Pembersihan & Transformasi

**Pembersihan:**
- Menghapus baris duplikat dan kolom tidak relevan
- Menangani missing value (hapus / imputasi)
- Standarisasi penulisan nama provinsi (e.g., `"JAKARTA"` → `"Jakarta"`)
- Memastikan tipe data konsisten (tahun: integer, penduduk & sampah: numerik)

**Transformasi:**
- Merge dataset berdasarkan `provinsi` dan `tahun`
- Feature engineering:
  - **Sampah per Kapita** (kg/orang) = Total Timbulan Sampah ÷ Jumlah Penduduk
  - **Total Pencemaran** = Total desa yang tercemar (air + tanah + udara)
- Konversi satuan dari ton ke kilogram

### 3. Load — Penyimpanan ke Database

- **Target:** PostgreSQL (hosted di Aiven)
- **Tabel utama:** `data_lingkungan`
- **Skema kolom:**

| Kolom | Tipe |
|---|---|
| Provinsi | VARCHAR |
| Jumlah Penduduk 2024 | BIGINT |
| Timbulan Sampah Tahunan (kg) | FLOAT |
| Pencemaran Air | INT |
| Pencemaran Tanah | INT |
| Pencemaran Udara | INT |

- **Proses:** CSV audit → SQLAlchemy insert → validasi duplikasi
- **Integritas:** Primary key gabungan `(provinsi, tahun)`, update otomatis jika data berubah

---

##  Machine Learning

- **Model:** Regresi Linear (Scikit-learn)
- **Tujuan:** Prediksi pengelolaan sampah berdasarkan jumlah penduduk dan tingkat pencemaran
- **Serving:** Visualisasi interaktif melalui Streamlit

---

## Teknologi yang Digunakan

| Kategori | Tools |
|---|---|
| ETL | Python, Selenium, Pandas, NumPy, SQLAlchemy |
| Machine Learning | Scikit-learn |
| Database | PostgreSQL (Aiven) |
| Visualisasi | Matplotlib, Seaborn, Streamlit |

---

## Struktur Proyek

```
UAS_DATAENGGINEERING/
├── UAS_DATA_ENGGINEER_1.ipynb   # ETL Pipeline
├── ML.ipynb                      # Machine Learning
└── streamlit_app.py              # Aplikasi Visualisasi
```

---

## Link Proyek

| Bagian | Link |
|---|---|
| ETL Pipeline | [UAS_DATA_ENGGINEER_1.ipynb](https://github.com/ardhiyondaputra/UAS_DATAENGGINEERING/blob/main/UAS_DATA_ENGGINEER_1.ipynb) |
| Machine Learning | [ML.ipynb](https://github.com/ardhiyondaputra/UAS_DATAENGGINEERING/blob/main/ML.ipynb) |
| Streamlit App | [streamlit_app.py](https://github.com/ardhiyondaputra/UAS_DATAENGGINEERING/blob/main/streamlit_app.py) |
| Looker |

---

## Lisensi

Proyek ini dibuat untuk keperluan akademik — **UAS Data Engineering**, Kelompok 8.
