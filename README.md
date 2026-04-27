# 🌡️ Analisis & Prediksi Data Cuaca IoT

> Proyek Mata Kuliah **Kecerdasan Buatan** — Kelompok 1, Kelas TI-1C

Analisis data time series dari sensor IoT yang dipasang di **teras outdoor** menggunakan Python. Mencakup eksplorasi data (EDA), penanganan missing values akibat gangguan jaringan dengan **imputasi data eksternal**, hingga prediksi suhu menggunakan Regresi Linear, Random Forest, ARIMA, dan ARIMAX.

---

## 👥 Anggota Kelompok

| No | Nama | NIM |
|----|------|-----|
| 1  | *Azka Galuh Basuki* | *4.33.25.2.04* |
| 2  | *Dela Fajar Mulia* | *4.33.25.2.07* |
| 3  | *Jatmiko Satrio Wibowo* | *4.33.25.2.11* |
| 4  | *Muhammad Fadhil* | *4.33.25.2.15* |
| 5  | *Ulfan Nayaka Dipta* | *4.33.25.2.23* |

> **Kelas:** TI-1C &nbsp;|&nbsp; **Kelompok:** 1 &nbsp;|&nbsp; **Mata Kuliah:** Kecerdasan Buatan

---

## 📁 Struktur Repository

```
├── 📁 dataset
│   ├── 📁 externals
│   │   ├── 📄 open-meteo.csv
│   │   └── 📄 visual-crossing.csv
│   └── 📁 sensors
│       ├── 📄 DataCuaca-Data-1.csv
│       ├── 📄 sensor_data_2.csv
│       └── 📄 sensor_data_3.csv
├── 📝 README.md
├── 📄 weather_analysis_1.ipynb
├── 📄 weather_analysis_2.ipynb
└── 📄 weather_analysis_3.ipynb
```

---

## 🔧 Spesifikasi Sensor

| Komponen | Keterangan |
|---|---|
| Sensor suhu & kelembapan | DHT11 |
| Sensor cahaya | LDR Module |
| Interval pengambilan data | 5 menit |
| Penyimpanan | PostgreSQL (Supabase) |
| Lokasi sensor | Teras |

---

## 📊 Dataset

### Dataset Primer — `sensor_data_3.csv`

| Kolom | Tipe | Keterangan |
|-------|------|-----------|
| `id` | int | ID unik record |
| `created_at` | datetime | Timestamp UTC (GMT+0) dari backend Supabase |
| `suhu` | float | Suhu udara (°C) |
| `kelembapan` | float | Kelembapan relatif (%) |
| `cahaya` | int | Nilai ADC sensor LDR (0–4095) |
| `kondisi` | string | `TERANG` / `GELAP` — ditentukan firmware |

### Dataset Eksternal

| File | Sumber | Keterangan |
|------|--------|-----------|
| `open-meteo.csv` | Open-Meteo API | Suhu, kelembapan, radiasi, tutupan awan — hourly, GMT+7 |
| `visual-crossing.csv` | Visual Crossing | Suhu, kelembapan, solar radiation, angin, tekanan — hourly, GMT+7 |

### ⚠️ Catatan Penting Sensor

- **Lokasi:** Teras outdoor — terpapar sinar matahari langsung
- **Periode:** 13–22 April 2026 · 2.082 record · interval ~5 menit
- **Timezone:** Kolom `created_at` dalam **UTC (GMT+0)**, dikonversi ke **WIB (GMT+7)** saat preprocessing
- **Sensor LDR terbalik:** Nilai `cahaya` raw **tinggi = gelap**, **rendah = terang** (karakteristik fisika LDR). Koreksi: `cahaya_terkoreksi = 4095 - cahaya_raw`
- **Gap besar:** 2 gap >1 jam (~15.4 jam pada 13–14 Apr, ~12.7 jam pada 18–19 Apr) akibat gangguan jaringan → diisi dari **data eksternal**
- **Gap kecil:** 3 gap (30–38 menit) → diisi **interpolasi linear**

---

## 📓 Notebook

### `weather_analysis_3.ipynb` — Analisis Utama

| # | Bagian | Deskripsi |
|---|--------|-----------|
| 1 | Import Library | pandas, numpy, matplotlib, seaborn, statsmodels, sklearn |
| 2 | Load & Inspect | Muat 3 sumber data, cek struktur dan tipe kolom |
| 3 | Preprocessing | Konversi UTC→WIB, koreksi nilai LDR, sorting |
| 4 | Audit Kualitas Data | Deteksi gap besar & kecil, distribusi jitter interval |
| 5 | **Imputasi Cerdas** | Gap besar → data eksternal (Open-Meteo + Visual Crossing, dengan kalibrasi offset); Gap kecil → interpolasi linear |
| 6 | Feature Engineering | Fitur temporal, siklus sin/cos, flag siang/malam, fitur eksternal |
| 7 | EDA | Univariat · Bivariat · Multivariat · Time-Series Story · Heatmap Jam×Tanggal |
| 8 | Deteksi Outlier | IQR + Z-Score, visualisasi pada timeline |
| 9 | Prediksi Suhu | Linear Regression · Random Forest · ARIMA(2,1,2) WF · ARIMAX(2,1,2) WF |
| 10 | Kesimpulan | Ringkasan temuan, perbandingan model, keterbatasan, rekomendasi |

---

## 🔍 Temuan EDA

| Variabel | Temuan |
|----------|--------|
| **Suhu** | Rentang 24–47°C · Puncak rata-rata ~14:00–15:00 WIB · Terendah ~05:00 WIB |
| **Kelembapan** | Korelasi negatif kuat dengan suhu (r ≈ −0.97) · Malam >95% · Siang <45% |
| **Cahaya** | Setelah koreksi LDR: korelasi positif dengan suhu (r ≈ +0.72) — masuk akal secara fisik |
| **Transisi** | Sensor GELAP→TERANG ~06:00 WIB, TERANG→GELAP ~18:00 WIB |
| **Validasi Eksternal** | Sensor vs Open-Meteo R² > 0.7 · Offset sistematis +1.1–1.5°C wajar karena paparan langsung matahari |

---

## 🤖 Hasil Prediksi

| Model | Tipe | Pakai Data Eksternal | MAE | RMSE | R² |
|-------|------|----------------------|-----|------|-----|
| Linear Regression | ML | ✅ Ya (fitur) | ~0.41°C | ~0.58°C | ~0.95 |
| Random Forest | ML | ✅ Ya (fitur) | ~0.29°C | ~0.50°C | ~0.97 |
| ARIMA(2,1,2) Walk-Forward | Statistik | ❌ Tidak | ~0.40°C | ~0.60°C | ~0.95 |
| **ARIMAX(2,1,2) Walk-Forward** | **Statistik** | **✅ Ya (eksogen)** | **~0.24°C** | **~0.50°C** | **~0.98** |

**Random Forest** dan **ARIMAX** adalah model terbaik. Kunci keberhasilan: **fitur lag** (suhu 5–15 menit sebelumnya) sebagai prediktor utama, diperkuat data cuaca eksternal sebagai konteks.

---

## 🚀 Cara Menjalankan

### 1. Clone repository

```bash
git clone https://github.com/delafajarmulia/suhu-detector-ai.git
cd suhu-detector-ai
```

### 2. Install dependencies

```bash
pip install pandas numpy matplotlib seaborn statsmodels scikit-learn jupyter
```

### 3. Jalankan notebook

```bash
jupyter notebook weather_analysis_3.ipynb
```

> Pastikan ketiga file dataset berada di direktori yang sama dengan notebook.

---

## 🛠️ Tech Stack

![Python](https://img.shields.io/badge/Python-3.9+-3776AB?logo=python&logoColor=white)
![Jupyter](https://img.shields.io/badge/Jupyter-Notebook-F37626?logo=jupyter&logoColor=white)
![pandas](https://img.shields.io/badge/pandas-150458?logo=pandas&logoColor=white)
![scikit--learn](https://img.shields.io/badge/scikit--learn-F7931E?logo=scikit-learn&logoColor=white)

| Library | Kegunaan |
|---------|---------|
| `pandas` | Manipulasi data, reindex, resample |
| `numpy` | Komputasi numerik, fitur siklus |
| `matplotlib` & `seaborn` | Visualisasi statis |
| `statsmodels` | Model ARIMA, ARIMAX (SARIMAX), ACF/PACF |
| `scikit-learn` | Regresi Linear, Random Forest, evaluasi model |

---

## ⚠️ Keterbatasan

- Gap besar diisi estimasi data eksternal — bukan data pengukuran sensor nyata
- Sensor teras terpapar langsung sinar matahari (offset suhu ~1–2°C di atas udara bebas)
- 10 hari pengamatan belum cukup untuk menangkap pola mingguan
- Data eksternal hourly di-upsample ke 5 menit → ada efek smoothing
- ARIMAX menggunakan rolling window 500, bukan expanding window penuh (trade-off kecepatan vs akurasi)

---

## 📌 Lisensi

Proyek ini dibuat untuk keperluan akademik. Bebas digunakan sebagai referensi pembelajaran.

---

<p align="center">
  Dibuat dengan ❤️ oleh <strong>Kelompok 1 · Kelas TI-1C</strong> · 2026
</p>
