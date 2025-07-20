<h1> Analisis Kasus Tidak Tap-Out Transjakarta </h1>

## 1. Project Overview
Project ini menganalisis data transaksi perjalanan Transjakarta untuk mendeteksi dan memahami **insiden "tidak tap-out"**. Analisis ini bertujuan membantu **Tim IT/QA dan Operasional Transjakarta** dalam mengidentifikasi pola error, prioritas perbaikan sistem gate, serta meningkatkan kepuasan penumpang.
Masalah ini dapat:
* Mengurangi akurasi data perjalanan (rute & arus penumpang).
* Mengganggu perhitungan pendapatan (potensi kerugian atau overcharge).
* Meningkatkan komplain penumpang (saldo berkurang, tarif default).
* Menyulitkan Tim IT/QA dan Operasional untuk perbaikan sistem dan perencanaan.
* Analisis ini dilakukan agar Transjakarta dapat mengidentifikasi pola error, prioritaskan perbaikan gate dan sistem, dan meningkatkan kepuasan serta kepercayaan pengguna.

**Key Objectives:**
1. Menghitung persentase total perjalanan dengan *tap-out* yang hilang (tidak tercatat), beserta **confidence interval 95%**.
2. Mengidentifikasi **5 halte asal** dengan jumlah insiden tidak tap-out tertinggi.
3. Menganalisis distribusi insiden berdasarkan **kategori usia penumpang** (Anak-anak, Remaja, Dewasa, Pra-Lansia, Lansia) dan **jenis kartu pembayaran** (TapCash, Brizzi, JakCard, e-Money, Flazz, QRIS), menggunakan **Chi-square/proportion test**.
4. Membandingkan proporsi insiden pada **jenis layanan bus** (Reguler, Royaltrans, Mikrotrans).
5. Mengidentifikasi **3 koridor** dengan insiden tertinggi.
6. Menguji hubungan insiden tidak tap-out dengan **error pembayaran (payAmount kosong)**.
7. Menganalisis **pola temporal** (harian & mingguan) insiden dalam periode analisis.
8. Menyusun **dashboard interaktif (1300x800)** untuk visualisasi KPI, tren, distribusi segmen, dan peta halte rawan.

## 2. Data Sources
- **Dataset Transjakarta (CSV)** – Berisi kolom:
  - Informasi transaksi: `transID`, `tapInTime`, `tapOutTime`, `payAmount`.
  - Profil pelanggan: `payCardID`, `payCardBank`, `payCardSex`, `payCardBirthDate`.
  - Rute & lokasi halte: `corridorID`, `corridorName`, `tapInStops`, `tapOutStops`, koordinat halte.
  - Informasi layanan bus: `direction`, `stopStartSeq`, `stopEndSeq`.
- Sumber data internal Transjakarta (periode 1 bulan).

**Deskripsi Kolom:**
- transID		: ID unik untuk setiap transaksi penumpang
- payCardID		: ID utama kartu pelanggan yang digunakan untuk tap masuk dan tap keluar
- payCardBank		: Bank penerbit kartu
- payCardName		: Nama pemilik kartu seperti yang tertulis pada kartu
- payCardSex		: Jenis kelamin pemilik kartu
- payCardBirthDate	: Tahun lahir pemilik kartu
- corridorID		: ID koridor atau rute Transjakarta (sebagai kunci pengelompokan rute)
- corridorName		: Nama koridor/rute yang menunjukkan titik awal dan akhir perjalanan
- direction		: Arah perjalanan (0 = pergi, 1 = pulang)
- tapInStops		: ID halte tap in
- tapInStopsName	: Nama halte tap in
- tapInStopsLat		: Latitude halte tap in
- tapInStopsLon		: Longitude halte tap in
- stopStartSeq		: Urutan halte saat tap in
- tapInTime		: Waktu saat penumpang melakukan tap in (tanggal dan jam)
- tapOutStops		: ID halte tap out
- tapOutStopsName	: Nama halte tap out
- tapOutStopsLat	: Latitude halte tap out
- tapOutStopsLon	: Longitude halte tap out
- stopEndSeq		: Urutan halte saat tap out
- tapOutTime		: Waktu saat penumpang melakukan tap out (tanggal dan jam)
- payAmount		: Jumlah pembayaran oleh penumpang (bisa nol untuk penumpang gratis)


## 3. Technologies Used
- **Bahasa Pemrograman:** Python (Pandas, NumPy, SciPy)
- **Visualisasi:** Matplotlib, Seaborn, Plotly
- **Dashboard Interaktif:** Tableau (opsional: Streamlit)
- **Analisis Statistik:** Chi-square, Proportion Z-test
- **Version Control:** Git
- **Lingkungan Kerja:** Jupyter Notebook

## 4. Project Structure
├── README.md <- Dokumentasi project ini.
│
├── data
│ ├── raw <- Data mentah dari sistem Transjakarta. ---> file upload terpisah
│ └── cleaned <- Data setelah proses cleaning (flagging & imputasi). ---> file upload terpisah
│
├── notebooks <- Jupyter notebooks untuk analisis.
│ ├── 1.0-fsm-data-cleaning.ipynb
│ ├── 2.0-fsm-eda-tapout.ipynb
│ ├── 3.0-fsm-statistical-test.ipynb
│ └── 4.0-fsm-dashboard-prep.ipynb
│
├── reports
│ ├── slide <- Laporan presentasi (PowerPoint)
│ └── figures <- Grafik & visualisasi untuk laporan.
│
├── requirements.txt <- Dependencies (pip freeze > requirements.txt).
│
└── src <- Script Python (fungsi cleaning, flagging, analisis, dsb).


## 5. Summary of Findings

### 5.1 Business Insight

1. **Proporsi insiden tidak tap-out rendah dan terkendali**  
   - Dari **37.900 perjalanan di April 2023**, hanya **1.344 kasus (3,55%)** yang tidak tap-out.  
   - **95% CI:** 3,36–3,73%.  
   - **Z-test (Z = -15,30, p < 0,001)** menunjukkan proporsi ini **signifikan lebih rendah dari ambang batas 5%**, menandakan insiden tap-out **terkendali**.

2. **Halte dan koridor tertentu menjadi titik rawan, meski sebagian besar insiden tersebar proporsional**  
   - **Halte Bkn** memiliki **jumlah insiden tertinggi (10 kasus, 5,78%)**, tetapi **tidak signifikan lebih tinggi** dibanding rata-rata halte lain (p = 0,0556).  
   - **Museum Fatahillah (12,7%)**, **Smk 57 (8,0%)**, dan **Puri Beta 2 (7,3%)** menonjol berdasarkan **proporsi insiden**, meskipun volumenya kecil.  
   - **Koridor Kebayoran Lama – Tanah Abang** memiliki **proporsi insiden signifikan (5,41%)** dibanding koridor lain (**p = 0,0308**) setelah data "Tidak Diketahui" dibersihkan.

3. **Segmen pelanggan dan jenis kartu tidak memengaruhi insiden secara signifikan**  
   - **Lansia (4,60%)** dan pengguna **Flazz (3,96%)** memiliki proporsi insiden tertinggi, tetapi **tidak signifikan** (p = 0,053 dan p = 0,093).  
   - **Mayoritas kasus absolut** berasal dari **Dewasa (744 kasus)** dan **JakCard (685 kasus)**, namun ini **sejalan dengan volume perjalanan**.

4. **Jenis layanan bus bukan faktor penentu insiden**  
   - Tidak ada perbedaan signifikan dalam **jumlah absolut maupun proporsi insiden** antara **Mikrotrans, Royaltrans, dan Transjakarta** (p-value semua > 0,3).  
   - Kategori **“Tidak Diketahui”** cenderung menunjukkan **masalah data** (pencatatan rute/layanan), bukan masalah operasional.

5. **Insiden tidak berkaitan dengan error pembayaran**  
   - **Chi-square (p = 0,6299)** dan **Cramér’s V (0,0025)** menunjukkan **hampir tidak ada korelasi** antara insiden *tidak tap-out* dan *error pembayaran*.  
   - Kedua masalah **terjadi independen** dan harus **ditangani terpisah**.

6. **Pola waktu perjalanan mendukung prioritas pemantauan, meski proporsi insiden stabil**  
   - Volume perjalanan **tinggi di weekday (1.600–2.000 perjalanan/hari)** dan **jam sibuk (06:00–09:00, 16:00–19:00)**.  
   - **Weekend turun drastis (300–400 perjalanan/hari)**.  
   - **Proporsi insiden tidak tap-out stabil**, tanpa lonjakan signifikan atau outlier harian.

---

### 5.2 Actionable Recommendation
1. **Fokus investigasi pada lokasi dengan proporsi insiden tinggi**  
   - Prioritaskan **Museum Fatahillah (12,7%)**, **Smk 57 (8,0%)**, dan **Puri Beta 2 (7,3%)**.  
   - **Koridor Kebayoran Lama – Tanah Abang** harus menjadi **prioritas audit teknis** karena insidennya **signifikan (5,41%)**.

2. **Perbaiki kualitas data operasional**  
   - Bersihkan kategori **“Tidak Diketahui”** (koridor, jenis layanan).  
   - Pastikan **sinkronisasi log tap-in/tap-out** agar investigasi lokasi error lebih akurat.

3. **Pisahkan penanganan masalah tap-out dan error pembayaran**  
   - Buat **alur investigasi terpisah** karena kedua isu **tidak berkaitan**.  
   - Fokus tap-out pada **perangkat gate & perilaku penumpang**, dan error pembayaran pada **sinkronisasi bank/API**.

4. **Fokus pengawasan pada periode volume tinggi, bukan proporsi insiden**  
   - Tambah **personel monitoring** di **weekday** dan **jam sibuk** (pagi & sore).  
   - Tujuannya mencegah **gangguan massal** meskipun proporsi insiden stabil.

5. **Segmentasi edukasi penumpang untuk kelompok rawan**  
   - **Lansia (4,60%)** dan pengguna **Flazz (3,96%)** bisa mendapat **edukasi khusus** atau **dukungan teknis sederhana** (signage, petugas bantuan).

6. **Monitoring dan analisis lanjutan**  
   - Lakukan **analisis bulanan** untuk deteksi tren (liburan, cuaca, event).  
   - Gunakan **dashboard real-time** agar **anomali bisa cepat ditindak**.

---

## 6. Contact
- **Nama:** Fransiska Sri Mayawi  
- **Email:** siskatho17@gmail.com 
- **LinkedIn:** (https://www.linkedin.com/in/fransiskasrimayawi/) 

