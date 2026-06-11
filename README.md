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

### 1. Extract (Pengambilan Data)

| Dataset | Sumber | Metode |
|---|---|---|
| Data Kasus DBD | [KEMENKES](https://www.kemkes.go.id/id/category/profil-kesehatan) | JSON API |
| Data Air Layak| [BPS](https://www.bps.go.id/id/statistics-table/2/ODU0IzI=/persentase-rumah-tangga-yang-memiliki-akses-terhadap-sumber-air-minum-layak-menurut-provinsi-dan-klasifikasi-desa--persen-.html) | Scraping HTML (Selenium) |

---
### 2. Transform (Pembersihan & Transformasi)

**Pembersihan:**
- Menghapus data rusak: Membuang baris data yang kosong (missing value) ataupun data ganda.
- Menyeragamkan format teks: Mengubah nama provinsi menjadi huruf kapital semua dan memperbaiki singkatan (contoh: "KEP. RIAU" menjadi "KEPULAUAN RIAU").
- Membuang data tidak relevan: Menghapus baris data total nasional ("INDONESIA") agar fokus pada data per provinsi.
- Memperbaiki tipe data: Memastikan kolom angka (seperti persentase air bersih) terbaca dengan benar sebagai tipe data numerik.

**Transformasi:**
- Menambahkan label waktu: Membuat kolom baru berisi keterangan tahun (2021-2024) pada setiap baris data.
- Menyaring kolom: Hanya mempertahankan kolom yang benar-benar penting untuk dianalisis.
- Menggabungkan data: Menyatukan tabel DBD dan tabel Air Bersih menjadi satu tabel utuh berdasarkan kesamaan Provinsi dan Tahun.
- Merapikan hasil akhir: Mengganti nama kolom yang acak menjadi lebih jelas, lalu menyimpannya sebagai file data gabungan yang siap pakai.
  
---
### 3. Load (Penyimpanan ke Database)

* #### Target
    - Sebuah tabel baru (misalnya diberi nama dbd_air_bersih) di dalam database pada server Aiven. Tabel ini merupakan output utama yang memuat data gabungan antara kelayakan sanitasi air dan jumlah kasus DBD. Tabel ini dapat diakses untuk melakukan analisis langsung dari database.
 
* #### Metode
    - Fungsi to_sql() dari pandas digunakan untuk menulis data dari DataFrame langsung ke tabel di database PostgreSQL
    - konfigurasi fungsi to_sql() diatur dengan parameter:
        * name: Untuk menentukan nama tabel tujuan di database.
        * con: Untuk menghubungkan sistem ke server Aiven menggunakan objek engine dari SQLAlchemy.
    - Data di cek keberhasilan pemindahannya dengan menarik 5 baris pertama dari database menggunakan perintah pd.read_sql() dan menampilkannya melalui df.head().

---

##  Arsitektur / Workflow ETL

* #### Alur Modular:
  - Fungsi Terpusat: Seluruh proses ETL dibungkus dalam satu fungsi utama (misalnya transformasi()) yang secara otomatis menjalankan tahapan pembacaan, pembersihan, penanganan nilai kosong, hingga penggabungan data.

  - Eksekusi Sekuensial: Kode disusun secara berurutan dan terstruktur agar alur datanya mudah diikuti, dijalankan, dan dievaluasi langsung di dalam notebook Google Colab.
    
* #### Tools yang Digunakan:
  - Python 3.x
  - Platform: Google Colab
  - Library: pandas, numpy, sqlalchemy, matplotlib, seaborn, sklearn, scipy

---

## Kode Program
* #### Struktur Kode:
  - Terdapat 2 notebook: ETL dan Machine Learning
  - Nama Variabel dan fungsi deskriptif: df_merge, clean_dataframe(), Jumlah_Kasus, Akses_Air_Minum, dll.
    
* #### Machin Leraning:
  
* #### Link Proyek:
  - ETL Pipeline:
    
  - Machine Learning:
    
  - Looker:

---
