# 🌡️ EDA Data Cuaca IoT — Suhu Detector AI

Proyek analisis data **time series** cuaca dari sensor IoT buatan sendiri, sebagai bagian dari tugas mata kuliah **Kecerdasan Buatan**.

---

## 📡 Tentang Proyek

Data dikumpulkan dari sensor IoT yang dibangun secara mandiri, mencatat **rata-rata suhu, kelembapan, dan intensitas cahaya setiap 5 menit** secara otomatis ke Google Sheets. Data kemudian diekspor ke CSV dan dianalisis menggunakan Python di Google Colab.

---

## 📁 Struktur Repo

```
├── 📁 dataset
│   └── 📄 DataCuaca-Data-1.csv
├── 📝 README.md
└── 📄 weather_analysis_1.ipynb
```

---

## 🔧 Spesifikasi Sensor

| Komponen | Keterangan |
|---|---|
| Sensor suhu & kelembapan | DHT11 |
| Sensor cahaya | LDR |
| Interval pengambilan data | 5 menit |
| Penyimpanan | Google Sheets → CSV |
| Lokasi sensor | Dalam ruangan (kamar) |

---

## 📊 Dataset

- **Jumlah record:** 328 baris
- **Rentang waktu:** 25 Maret 2026 – 27 Maret 2026 (~2.7 hari)
- **Kolom:**

| Kolom | Tipe | Keterangan |
|---|---|---|
| `timestamp` | datetime | Waktu pencatatan |
| `suhu` | float | Suhu dalam °C |
| `kelembapan` | float | Kelembapan relatif (%) |
| `cahaya` | int | Intensitas cahaya (lux) |
| `kondisi` | string | Label: `TERANG` / `GELAP` |

### ⚠️ Keterbatasan Data

- **Missing data:** Terdapat 49 gap akibat koneksi internet yang tidak stabil, ditangani dengan interpolasi linear (suhu & kelembapan) dan forward fill (cahaya)
- **Cahaya artifisial:** Karena sensor berada di dalam kamar, nilai cahaya malam hari bisa tinggi akibat lampu kamar menyala — sudah di-flag di notebook

---

## 📓 Isi Notebook

1. **Import Library** — pandas, numpy, matplotlib, seaborn, statsmodels, sklearn
2. **Load & Inspect Data** — overview data mentah
3. **Preprocessing** — parsing timestamp, konversi desimal, sorting
4. **Handling Missing Values** — reindex 5 menit, deteksi gap, imputasi
5. **Feature Engineering** — ekstraksi jam, flag malam, deteksi cahaya artifisial
6. **EDA**
   - Univariat: distribusi tiap variabel
   - Time series: tren temporal + rolling mean
   - Bivariat: heatmap korelasi, scatter plot
   - Multivariat: pola harian (diurnal pattern), pair plot
7. **Deteksi Outlier** — IQR method + boxplot
8. **Prediksi Suhu**
   - Model A: Regresi Linear (fitur temporal + sin/cos)
   - Model B: ARIMA(2,1,2)
   - Perbandingan MAE, RMSE, R²
9. **Kesimpulan**

---

## 🚀 Cara Menjalankan

1. Buka [Google Colab](https://colab.research.google.com)
2. Load notebook dari repo ini: **File → Open notebook → GitHub → cari repo ini**
3. Upload file `DataCuaca_-_Data.csv` ke sidebar Files Colab
4. Jalankan sel dari atas ke bawah (**Runtime → Run all**)

### Requirements

Semua library sudah tersedia di Google Colab secara default. Tidak perlu install tambahan.

```
pandas · numpy · matplotlib · seaborn · scikit-learn · statsmodels · plotly
```

---

## 📈 Hasil Singkat

| Model | MAE | RMSE | R² |
|---|---|---|---|
| Regresi Linear (dengan fitur lag) | 0.0875°C | 0.1233°C | 0.9759 |
| ARIMA(2,1,2) Walk-Forward | 0.0556°C | 0.1205°C | 0.9769 |

---

## 👤 Author

**Dela Fajar Mulia**  
Mahasiswa — Mata Kuliah Kecerdasan Buatan
