<h1> Blue Bird: Analisis Kegagalan Layanan di CBD dan Produktivitas Armada </h1>

<div align="center">

🌐 [English](README.md) | [Indonesia](README_id.md)

[![Python](https://img.shields.io/badge/python-3.14%2B-blue)]()

</div>

## 1. Project Overview
Proyek ini menganalisis data operasional taksi Blue Bird selama tiga bulan (Oktober–Desember 2023) sebagai capstone Data Analysis. Blue Bird mengoperasikan armada fisik dengan pengemudi tetap, sehingga setiap jam mobil bertugas tanpa penumpang adalah biaya. Brief proyek menyebut tiga dugaan: produktivitas armada (*ritase*, jumlah trip per pengemudi per hari) mungkin menurun, permintaan pelanggan tidak selalu terpenuhi, dan sebagian trip eVoucher korporat terjadi pada waktu yang tidak lazim.

Fokus utamanya adalah **mencari kapan dan di mana request pelanggan gagal, serta bagaimana pasokan pengemudi dan jadwal shift berhubungan dengan kegagalan tersebut**, terutama di CBD pada jam puncak. Produktivitas armada dan pemakaian eVoucher juga ikut diperiksa.

Tujuan Utama:
- **Optimasi armada:** mengukur trip per shift, idle time, dan perbedaan antar pool, serta menguji apakah *ritase* benar-benar menurun.
- **Keandalan layanan:** memetakan request yang gagal per jam, zona, dan kanal pemesanan, lalu membandingkannya dengan pasokan pengemudi per jam.
- **Kontrol B2B:** menggambarkan pola pemakaian eVoucher dan menandai trip yang perlu ditinjau, tanpa menganggapnya sebagai kecurangan.
- **Keandalan data:** membersihkan dan memvalidasi data argometer dan layanan sebelum dianalisis, sesuai permintaan brief.

## 2. Data Sources
Enam tabel dari database MySQL yang disediakan untuk kelas (tidak tersedia untuk publik):
- `bb_trips` – Trip yang selesai: waktu, jarak, tarif, metode bayar, jenis layanan, kanal pemesanan (318.098 baris)
- `bb_service_requests` – Request pelanggan dari App dan Call Center, beserta status dan waktu tunggu (289.622 baris)
- `bb_driver_shifts` – Shift pengemudi: clock-in/out, jumlah trip, menit idle (252.524 baris)
- `bb_drivers` – Data pengemudi: pool, status kerja, tipe kendaraan, rating (4.055 baris)
- `bb_pools` – Lokasi pool (garasi) dan afinitas zonanya (8 baris)
- `bb_corporate_clients` – Klien korporat pengguna eVoucher dan industrinya (286 baris)

## 3. Technologies Used
- Bahasa pemrograman: Python (pandas, NumPy)
- Visualisasi: Matplotlib
- Statistik: SciPy, statsmodels
- Akses database: PyMySQL, python-dotenv (kredensial dibaca dari file `.env` lokal berisi `USER`, `PASS`, dan `DATABASE`)
- Lainnya: Jupyter Notebook

## 4. Project Structure

```
├── README.md                      <- Versi bahasa Inggris dari file ini.
│
├── README_id.md                   <- File ini (bahasa Indonesia).
│
├── analisis_bluebird_MAIN.ipynb   <- Notebook utama: pemahaman bisnis, pembersihan data,
│                                     eksplorasi, visualisasi, export, dan kesimpulan.
│
└── outputs                        <- Dibuat saat notebook dijalankan.
    ├── raw                        <- Enam tabel sumber persis seperti saat dimuat (CSV).
    ├── cleaned                    <- Tabel bersih yang dipakai dalam analisis (CSV).
    ├── exceptions                 <- Record yang dikeluarkan atau ditandai beserta alasannya, dan daftar tinjauan eVoucher.
    ├── reports                    <- Laporan kualitas data dan ringkasan uji statistik.
    └── charts                     <- Grafik dari Part 3 (PNG).
```

File CSV export yang lebih besar dari 25 MB dipecah menjadi beberapa bagian bernomor (misalnya `bb_trips_cleaned_part1.csv` sampai `bb_trips_cleaned_part5.csv`) dengan header yang sama, sehingga bisa dibuka satu per satu atau digabung kembali sesuai urutan.

Notebook utama, [`analisis_bluebird_MAIN.ipynb`](analisis_bluebird_MAIN.ipynb), terdiri dari empat bagian:
1. **Data Cleaning:** menstandarkan label layanan, menandai data bermasalah, dan mengeluarkan 7.091 trip (2,2%) dengan jarak negatif atau delay booking-ke-meter negatif. Tabel lain tidak dihapus barisnya dan memakai kolom flag.
2. **Data Exploration:** kegagalan request, pasokan pengemudi, utilisasi, pool, pola eVoucher, dan uji statistik.
3. **Data Visualization:** grafik untuk temuan utama.
4. **Exporting:** file raw, cleaned, dan exception.

## 5. Summary of Finding
### 5.1 Business Insight
- **Request yang gagal terpusat di CBD pada pukul 07–08 dan 17–18.** Secara keseluruhan 6,7% request gagal. Request CBD pada jam tersebut gagal 36,6%, dibanding 5,5% pada jam lain di CBD. Jumlahnya hanya 7,1% dari semua request, tetapi menyumbang 38,5% dari seluruh kegagalan. Pola yang sama muncul lagi di data Desember yang tidak dipakai untuk memilih jam (36,7% vs 5,5%).
- **Kegagalan pagi tidak dijelaskan oleh jumlah pengemudi yang bertugas.** Pengemudi bertugas naik dari sekitar 1.306 pada pukul 06.00 menjadi 2.322 pada pukul 08.00, tetapi kegagalan di CBD tetap tinggi. Penyebabnya tidak terlihat di data; dugaan masalah dispatch atau kesiapan pengemudi masih hipotesis yang membutuhkan log dispatch.
- **Puncak sore bertepatan dengan pergantian shift.** Shift pagi berakhir sementara shift malam belum mulai. Skenario kapasitas (berdasarkan asumsi tingkat sibuk pengemudi) hanya menunjukkan kekurangan sekitar pukul 16.00–17.00, jadi ini masih hipotesis, bukan penyebab yang terbukti.
- **Request lewat App lebih sering gagal daripada Call Center** di setiap zona dan kelompok jam (22,45% vs 18,66% pada jam puncak kegagalan).
- **Ritase rendah tetapi tidak menurun.** Sekitar 1,26 trip per shift tanpa tren turun selama 12 minggu; pengemudi hanya 9,4% dari waktu bertugasnya membawa penumpang. Perbedaan antar pool hilang setelah komposisi zona diperhitungkan (F-test p = 0,62).
- **eVoucher:** 51,4% trip eVoucher dari Airport terjadi di akhir pekan, dibanding 17,6% untuk trip Airport lainnya. Tidak ditemukan bukti kecurangan, tetapi belum ada aturan pemakaian tertulis; 1.290 trip dimasukkan ke daftar tinjauan.

**Keterbatasan:** data hanya tiga bulan; semua hasil berupa asosiasi, bukan sebab-akibat yang terbukti; skala data dan polanya yang sangat rapi menunjukkan kemungkinan data sampel atau simulasi, sehingga angka absolut perlu dikonfirmasi; tidak ada log dispatch maupun data lokasi pengemudi.

### 5.2 Actionable Recommendation
1. **Periksa proses dispatch pagi sebelum menambah pengemudi:** cek log dispatch pukul 07–08, waktu dari clock-in sampai pengemudi siap menerima order, serta perbedaan proses App dan Call Center.
2. **Uji coba penundaan akhir shift pagi atau shift pendek tambahan (15.00–19.00) di CBD**, dibandingkan dengan pool yang tidak ikut uji coba.
3. **Uji coba pengurangan jam shift yang berlebih**, dimulai dari zona dengan pendapatan per jam terendah, hanya setelah skala data dikonfirmasi, sambil memantau kegagalan.
4. **Sepakati aturan eVoucher tertulis dengan klien korporat** (hari, jam, tujuan) dan tinjau 1.290 trip yang ditandai bersama admin klien.
5. **Nilai pool dengan trip per shift yang disesuaikan zona dan pendapatan per jam**, bukan dengan flag `ritase_target_met` (hanya tercapai pada 0,35% shift).
6. **Perbaiki pencatatan data:** samakan jam di aplikasi dan sistem lama, tolak jarak negatif saat input, dan pastikan tabel trip lengkap.

Baseline setiap KPI ada di notebook; nilai targetnya sebaiknya ditentukan oleh manajemen.

## 6. Contact
- Name: _(belum diisi)_
- Email: _(belum diisi)_
- Linkedin: _(belum diisi)_
