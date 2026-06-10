# 🗑️ Analisis Hubungan antara Timbulan Sampah, Pencemaran Lingkungan, dan Jumlah Penduduk di Indonesia

> Proyek Data Engineering — Kelompok 6

---

## 👥 Kontributor

| Nama Lengkap | NIM | Peran |
|---|---|---|
| Alridzki Innama N.R | 234311031 | Data Analyst |
| Ardhiyonda Wahyu P.V | 234311032 | Data Engineer |
| Arfan Bagus D. | 234311033 | Data Engineer |
| Ramizah Budi C.P | 234311049 | Project Manager |
| Satria Bagus A.I | 234311054 | Data Analyst |

---

## 📌 Deskripsi Proyek

Proyek ini dikembangkan untuk menganalisis **hubungan antara timbulan sampah, pencemaran lingkungan, dan jumlah penduduk di Indonesia** menggunakan teknik web scraping. Data kemudian melalui proses pembersihan dan transformasi, lalu dimuat ke dalam database **PostgreSQL**, dengan tujuan untuk mengungkap hubungan antar ketiga variabel tersebut.

---

## 🎯 Tujuan & Manfaat

**Tujuan:**
Membangun pipeline data yang mengotomatisasi proses pengumpulan, pengolahan, dan visualisasi data dari berbagai sumber untuk menganalisis hubungan antara jumlah penduduk, timbulan sampah, dan pencemaran lingkungan di Indonesia.

**Manfaat:**
- 📊 Memberikan wawasan berbasis data untuk mendukung kebijakan lingkungan
- 📚 Menjadi referensi bagi penelitian lanjutan di bidang kependudukan dan lingkungan
- 🌿 Meningkatkan kesadaran masyarakat terhadap dampak pertumbuhan penduduk
- 🛠️ Memberikan pengalaman praktis dalam penerapan teknik data engineering

---

## 🏗️ Arsitektur ETL Pipeline

```
📥 EXTRACT              🔄 TRANSFORM             📤 LOAD
─────────────          ─────────────────         ──────────────────
SIPSN (JSON)    ──►    Cleaning & Standar-  ──►  PostgreSQL (Aiven)
BPS (Selenium)  ──►    isasi Provinsi & ──►      └── tabel: data_lingkungan
KLHK (HTML)     ──►    Tahun, Feature Eng  ──►
                       Merge Dataset
                                                        │
                                                        ▼
                                               📊 Visualisasi (Streamlit)
                                               🤖 Machine Learning
```

---

## ⚙️ Pipeline Detail

### 1. 📥 Extract — Pengambilan Data

| Dataset | Sumber | Metode |
|---|---|---|
| Data Timbulan Sampah | [SIPSN KLHK](https://sipsn.menlhk.go.id/sipsn/public/data/timbulan) | JSON API |
| Pencemaran Lingkungan Hidup | [BPS](https://www.bps.go.id/id/statistics-table/2/OTU5IzI=/banyaknya-desa-kelurahan-menurut-jenis-pencemaran-lingkungan-hidup.html) | Scraping HTML (Selenium) |
| Jumlah Penduduk per Provinsi | [BPS Sulut](https://sulut.bps.go.id/id/statistics-table/2/OTU4IzI=/jumlah-penduduk-menurut-provinsi-di-indonesia.html) | Scraping HTML (Selenium) |

### 2. 🔄 Transform — Pembersihan & Transformasi

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

### 3. 📤 Load — Penyimpanan ke Database

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

## 🤖 Machine Learning

- **Model:** Regresi Linear (Scikit-learn)
- **Tujuan:** Prediksi pengelolaan sampah berdasarkan jumlah penduduk dan tingkat pencemaran
- **Serving:** Visualisasi interaktif melalui Streamlit

---

## 🛠️ Teknologi yang Digunakan

| Kategori | Tools |
|---|---|
| ETL | Python, Selenium, Pandas, NumPy, SQLAlchemy |
| Machine Learning | Scikit-learn |
| Database | PostgreSQL (Aiven) |
| Visualisasi | Matplotlib, Seaborn, Streamlit |

---

## 📁 Struktur Proyek

```
UAS_DATAENGGINEERING/
├── UAS_DATA_ENGGINEER_1.ipynb   # ETL Pipeline
├── ML.ipynb                      # Machine Learning
└── streamlit_app.py              # Aplikasi Visualisasi
```

---

## 🔗 Link Proyek

| Bagian | Link |
|---|---|
| 🔧 ETL Pipeline | [UAS_DATA_ENGGINEER_1.ipynb](https://github.com/ardhiyondaputra/UAS_DATAENGGINEERING/blob/main/UAS_DATA_ENGGINEER_1.ipynb) |
| 🤖 Machine Learning | [ML.ipynb](https://github.com/ardhiyondaputra/UAS_DATAENGGINEERING/blob/main/ML.ipynb) |
| 📊 Streamlit App | [streamlit_app.py](https://github.com/ardhiyondaputra/UAS_DATAENGGINEERING/blob/main/streamlit_app.py) |

---

## 📄 Lisensi

Proyek ini dibuat untuk keperluan akademik — **UAS Data Engineering**, Kelompok 6.
