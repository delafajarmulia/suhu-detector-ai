# 🌡️ Analisis & Prediksi Data Cuaca IoT

> Proyek Mata Kuliah **Kecerdasan Buatan** — Kelompok 1, Kelas TI-1C

Analisis data time series dari sensor IoT yang dipasang di **teras outdoor** menggunakan Python. Mencakup eksplorasi data (EDA), penanganan missing values akibat gangguan jaringan, hingga prediksi suhu menggunakan Regresi Linear dan ARIMA.

---

## 👥 Anggota Kelompok

| No | Nama | NIM |
|----|------|-----|
| 1  | *Azka Galuh Basuki* | *4.33.25.2.04* |
| 2  | *Dela Fajar Mulia* | *4.33.25.2..07* |
| 3  | *Jatmiko Satrio Wibowo* | *4.33.25.2.11* |
| 4  | *Muhammad Fadhil* | *4.33.25.2.15* |
| 5  | *Ulfan Nayaka Dipta* | *4.33.25.2.23* |

> **Kelas:** TI-1C &nbsp;|&nbsp; **Kelompok:** 1 &nbsp;|&nbsp; **Mata Kuliah:** Kecerdasan Buatan

---

## 📁 Struktur Repository

```
├── 📁 dataset
│   ├── 📄 DataCuaca-Data-1.csv
│   └── 📄 sensor_data_2.csv
├── 📝 README.md
├── 📄 weather_analysis_1.ipynb
└── 📄 weather_analysis_2.ipynb
```

---

## 🔧 Spesifikasi Sensor

| Komponen | Keterangan |
|---|---|
| Sensor suhu & kelembapan | DHT11 |
| Sensor cahaya | LDR Module |
| Interval pengambilan data | 5 menit |
| Penyimpanan | PostgreeSQL (Supabase) |
| Lokasi sensor | Teras |

---

## 📊 Dataset

**File:** `sensor_data_rows.csv`

| Kolom | Tipe | Keterangan |
|-------|------|-----------|
| `id` | int | ID unik record |
| `created_at` | datetime | Timestamp UTC (GMT+0) dari backend Supabase |
| `suhu` | float | Suhu udara (°C) |
| `kelembapan` | float | Kelembapan relatif (%) |
| `cahaya` | int | Nilai ADC sensor LDR (0–4095) |
| `kondisi` | string | `TERANG` / `GELAP` — ditentukan firmware |

### ⚠️ Catatan Penting Sensor

- **Lokasi:** Teras outdoor — terpapar sinar matahari langsung
- **Periode:** 13–18 April 2026 · 1.256 record · interval ~5 menit
- **Timezone:** Kolom `created_at` dalam **UTC (GMT+0)**, dikonversi ke **WIB (GMT+7)** saat preprocessing
- **Sensor LDR terbalik:** Nilai `cahaya` raw **tinggi = gelap**, **rendah = terang** (karakteristik fisika LDR). Koreksi dilakukan dengan `cahaya_terkoreksi = 4095 - cahaya_raw`
- **Gap data:** Terdapat gap ~15.4 jam (13–14 Apr) akibat gangguan jaringan, serta banyak jitter interval karena latensi

---

## 📓 Notebook

### `weather_analysis_2.ipynb` — Analisis Utama

| # | Bagian | Deskripsi |
|---|--------|-----------|
| 1 | Import Library | pandas, numpy, matplotlib, seaborn, statsmodels, sklearn |
| 2 | Load & Inspect | Muat data, cek struktur dan tipe kolom |
| 3 | Preprocessing | Konversi UTC→WIB, koreksi nilai LDR, sorting |
| 4 | Audit Kualitas Data | Deteksi gap jaringan, distribusi jitter interval |
| 5 | Handling Missing Values | Reindex 5 menit, interpolasi linear, forward fill |
| 6 | Feature Engineering | Fitur temporal, siklus sin/cos, flag siang/malam |
| 7 | EDA | Univariat · Bivariat · Multivariat · Time-Series Story · Transisi siang-malam |
| 8 | Deteksi Outlier | IQR method + Z-score, visualisasi pada timeline |
| 9 | Prediksi Suhu | Regresi Linear (lag) & ARIMA(2,1,2) Walk-Forward |
| 10 | Kesimpulan | Ringkasan temuan, keterbatasan, rekomendasi |

---

## 🔍 Temuan EDA

| Variabel | Temuan |
|----------|--------|
| **Suhu** | Rentang 24.1–47.9°C · Puncak rata-rata jam 15:00 WIB (~40°C) · Terendah jam 05:00 WIB (~25°C) |
| **Kelembapan** | Korelasi negatif kuat dengan suhu (r = −0.97) · Malam >95% · Siang <45% |
| **Cahaya** | Setelah koreksi LDR: korelasi positif dengan suhu (r ≈ +0.72) — masuk akal secara fisik |
| **Transisi** | Sensor GELAP→TERANG sekitar jam 06:00 WIB, TERANG→GELAP sekitar jam 18:00 WIB |

---

## 🤖 Hasil Prediksi

| Model | MAE | RMSE | R² |
|-------|-----|------|----|
| Regresi Linear (Lag + Siklus + Cahaya) | ~0.41°C | ~0.58°C | ~0.95 |
| ARIMA(2,1,2) Walk-Forward | ~0.39°C | ~0.60°C | ~0.95 |

Kedua model mencapai **R² > 0.95**. Kunci keberhasilan: **fitur lag** — suhu 5–15 menit sebelumnya adalah prediktor terkuat (autokorelasi tinggi pada data interval pendek).

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
jupyter notebook weather_analysis_2.ipynb
```

> Pastikan file `sensor_data_2.csv` berada di direktori yang sama dengan notebook.

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
| `statsmodels` | Model ARIMA, ACF/PACF |
| `scikit-learn` | Regresi Linear, evaluasi model |

---

## ⚠️ Keterbatasan

- Gap ~15.4 jam diisi interpolasi — bukan data pengukuran nyata
- 5 hari pengamatan belum cukup untuk menangkap pola mingguan
- Suhu ekstrem (~47°C) kemungkinan akibat paparan langsung ke badan sensor, bukan suhu udara
- Sensor LDR tidak terkalibrasi ke satuan lux standar

---

## 📌 Lisensi

Proyek ini dibuat untuk keperluan akademik. Bebas digunakan sebagai referensi pembelajaran.

---

<p align="center">
  Dibuat dengan ❤️ oleh <strong>Kelompok 1 · Kelas TI-1C</strong> · 2026
</p>
