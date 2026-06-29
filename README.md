<div align="center">

# 🤖 TRADING BOT v8.0.2 — AI MT5 Auto Trader

### *Kecerdasan Buatan untuk Keuntungan Nyata*

Bot trading **MetaTrader 5** otomatis berbasis **Multi-AI Ensemble** + **Analisa Teknikal** + **Filter Berita Fundamental**, lengkap dengan **Dashboard Realtime berbasis web** dan **manajemen risiko otomatis**.

![Python](https://img.shields.io/badge/Python-3.10%2B-blue?logo=python&logoColor=white)
![Platform](https://img.shields.io/badge/Platform-MetaTrader%205-orange?logo=metatrader)
![AI](https://img.shields.io/badge/AI-Ensemble%20Multi--Model-purple)
![License](https://img.shields.io/badge/License-Proprietary-red)
![Status](https://img.shields.io/badge/Status-Beta-yellow)

*Developed by **Iddant ID***

</div>

---

## 📑 Daftar Isi

1. [Sekilas Tentang Bot](#-sekilas-tentang-bot)
2. [Fitur Utama](#-fitur-utama)
3. [Cara Kerja & Alur Proses](#-cara-kerja--alur-proses)
4. [Arsitektur & Struktur Proyek](#-arsitektur--struktur-proyek)
5. [Instalasi](#-instalasi)
6. [Konfigurasi (.env & config.json)](#-konfigurasi)
7. [Menjalankan Bot & Daftar Menu](#-menjalankan-bot--daftar-menu)
8. [Penjelasan Modul & Fungsi](#-penjelasan-modul--fungsi)
9. [Manajemen Risiko](#-manajemen-risiko)
10. [Dashboard](#-dashboard)
11. [FAQ & Troubleshooting](#-faq--troubleshooting)
12. [Disclaimer](#-disclaimer)

---

## 🎯 Sekilas Tentang Bot

Bot ini adalah **sistem trading otomatis end-to-end** untuk MetaTrader 5. Bot mengunduh data harga (multi-timeframe), menghitung puluhan indikator teknikal, meminta keputusan dari **beberapa model AI sekaligus (ensemble)**, menyaring keputusan tersebut dengan **kalender berita Forex Factory**, lalu — bila semua syarat terpenuhi — **membuka order secara otomatis** dengan Stop Loss / Take Profit yang sudah dihitung berdasarkan risiko nominal (USD/IDR).

Koneksi ke MT5 dilakukan melalui **REST API gateway** (`https://mt5.mtapi.io`), sehingga bot **tidak perlu menjalankan terminal MT5 di mesin yang sama** — cukup kredensial login broker.

> **Untuk siapa?** Trader yang ingin otomatisasi penuh dengan kontrol risiko ketat, transparansi keputusan (lihat alasan tiap sinyal), dan monitoring realtime via browser.

---

## ✨ Fitur Utama

### 🧠 1. Multi-AI Ensemble Engine
Bot tidak bergantung pada satu AI. Beberapa provider memberikan "suara" (vote) BUY / SELL / WAIT, lalu diambil keputusan **konsensus**:

| Provider | Keterangan |
|---|---|
| **MiMo** (Xiaomi) | Model utama ensemble |
| **QwenCloud** (Alibaba DashScope) | OpenAI-compatible |
| **HebCode** | Endpoint Anthropic/OpenAI-compatible |
| **Gemini** (Google) | Opsional |
| **DeepSeek** | Opsional |
| **OpenRouter** | Gateway multi-model opsional |
| **Anthropic** (custom/proxy) | Opsional |

- **Mode `ensemble`**: gabungkan banyak model, ambil suara mayoritas + rata-rata confidence.
- **Mode single provider**: pilih satu AI saja.
- **`signal_strength_threshold`**: sinyal di bawah ambang ini otomatis jadi **WAIT** (tidak entry).
- **Custom Prompt (Rumus AI)**: Anda bisa menyuntik strategi/prompt sendiri (menu `[02]`).
- **Fallback otomatis**: jika AI error/timeout/403, bot jatuh ke keputusan murni Analisa Teknikal.

### 📊 2. Analisa Teknikal Multi-Timeframe (MTF)
Bot menganalisa **timeframe trading** (mis. H1) **dan** **timeframe lebih tinggi** (mis. D1) untuk menghormati tren besar:

- Indikator: **SMA(20/50), EMA(20), RSI(14), Stochastic, ADX(14), ATR(14), Bollinger Bands, Price Position**.
- **Filter ADX** (`adx_filter_level`): hindari entry saat pasar sideways/lemah.
- **Konfluensi HTF**: opsi menolak sinyal yang melawan tren timeframe tinggi.

### 📰 3. Filter Berita Fundamental (Forex Factory + NewsAPI)
- **Kalender Ekonomi Forex Factory** (feed `faireconomy`) — event high/medium/low impact minggu berjalan.
- **Proteksi high-impact**: bot menahan entry (WAIT) jika ada berita berdampak tinggi dalam jendela waktu tertentu (`ff_within_hours`).
- **NewsAPI**: pencarian artikel berita terkait instrumen (opsional).
- Filter mata uang otomatis dari simbol (mis. `XAUUSD` → memantau berita **USD**).

### 💰 4. Manajemen Risiko & Posisi Otomatis
- **Risiko nominal tetap** per posisi (`max_loss_per_trade_usd`) → ukuran SL dihitung dari ini, bukan asal pips.
- **Reward Ratio** (mis. 1:2.5) untuk TP otomatis.
- **SL/TP berbasis ATR** sebagai alternatif perhitungan.
- **Software TP/SL**: bot memantau & menutup posisi sendiri (tidak hanya andalkan server broker).
- **Break-Even (BEP)**: pindahkan SL ke titik impas setelah profit tertentu.
- **Trailing Stop**: kunci profit yang bertambah mengikuti harga.
- **Batas posisi**: `max_total_positions` & `max_positions_per_symbol`.
- **Konversi IDR**: jika akun dalam Rupiah, semua nominal risiko dikonversi otomatis.

### 🖥️ 5. Dashboard Realtime (Web) + Snapshot Terminal
- **Dashboard Realtime berbasis web** (HTTP server lokal, auto-refresh) — saldo, P/L, posisi, analisa AI, berita, performa, dan **chart harga market**.
- ⭐ **Auto-launch di belakang layar**: saat **Start Trading otomatis**, dashboard langsung terbuka di browser dan berjalan sebagai *background thread* — log trading tetap tampil di terminal. Dashboard ditutup otomatis saat trading berhenti.
- **Snapshot Terminal/HTML**: ringkasan cepat tanpa server.

### 🌐 6. Market Analysis dari Web (Yahoo Finance)
- **Performance**: perubahan harga (%, pips, net) lintas periode.
- **Market Overview**: pantau banyak instrumen FX & makro sekaligus.
- **Pemetaan simbol cerdas**: `XAUUSDm → GC=F` (emas), `BTCUSDm → BTC-USD`, `US30 → ^DJI`, dll — otomatis mengenali logam, energi, crypto, indeks, dan pasangan FX.

### 🔧 7. Pengaturan Tanpa Edit Manual
- **Edit Settings per-tombol**: ubah parameter apa pun dari menu interaktif.
- ⭐ **Change Symbol** (menu `[8]` di Edit Settings): ganti `symbol_base` & `symbol_broker` sekaligus tanpa membuka `config.json`.
- **Smart Preset**: terapkan profil siap-pakai (mis. *Swing High-Accuracy H1*) berdasarkan mode & saldo.
- **Profil config ganda**: jalankan `CONFIG_FILE=config3.json python main.py` tanpa menimpa config utama.

### 📈 8. Backtester
Uji strategi pada data historis sebelum dipakai live (menu `[13]`).

### 📓 9. Jurnal & Logging
- **Trade Journal**: catat setiap order.
- **Analysis Log**: rekam tiap keputusan analisa (untuk audit & evaluasi).

### 🔔 10. Notifikasi Telegram
Kirim notifikasi sinyal/eksekusi ke Telegram (opsional).

### 🔐 11. Proteksi Lisensi
Verifikasi lisensi (`LICENSE_KEY`) sebelum bot berjalan.

---

## ⚙️ Cara Kerja & Alur Proses

### Diagram Alur Loop Trading Otomatis

```
┌──────────────────────────────────────────────────────────────────┐
│  START TRADING (Menu [01] / [02])                                  │
│  └─► Dashboard Realtime dibuka otomatis di belakang layar 🖥️       │
└──────────────────────────────────────────────────────────────────┘
                              │
                              ▼
        ┌─────────────────────────────────────────┐
        │  LOOP setiap `run_interval_seconds`      │◄────────────┐
        └─────────────────────────────────────────┘             │
                              │                                  │
          ┌───────────────────┼───────────────────┐             │
          ▼                   ▼                   ▼              │
   1. Cek batas posisi   2. Cek pasar buka   3. Cek berita       │
      (max positions)       (is_market_open)    high-impact      │
          │                   │                   │              │
          └───────────────────┴───────────────────┘              │
                              │  (semua lolos)                   │
                              ▼                                  │
        4. Unduh data harga (Trading TF + Higher TF)             │
                              │                                  │
                              ▼                                  │
        5. Hitung indikator teknikal (SMA/RSI/ADX/ATR/…)         │
                              │                                  │
                              ▼                                  │
        6. Minta keputusan AI ENSEMBLE (vote BUY/SELL/WAIT)      │
                              │                                  │
                              ▼                                  │
        7. Gabungkan TA + AI + Berita → SINYAL FINAL             │
                              │                                  │
              ┌───────────────┴───────────────┐                 │
              ▼                               ▼                  │
         Sinyal = WAIT                  Sinyal = BUY/SELL        │
              │                               │                 │
              │                               ▼                 │
              │            8. Hitung SL/TP (risiko USD + RR)     │
              │                               │                 │
              │                               ▼                 │
              │            9. EKSEKUSI ORDER (place_order)       │
              │                               │                 │
              │                               ▼                 │
              │           10. Selaraskan tampilan analisa        │
              │               dengan harga & order nyata         │
              └───────────────┬───────────────┘                 │
                              ▼                                  │
        11. Monitor posisi (BEP / Trailing / Software TP-SL) ────┘
```

### Penjelasan Langkah

1. **Guard posisi** — pastikan belum melebihi `max_total_positions` / `max_positions_per_symbol`.
2. **Guard pasar** — `is_market_open()`: jika market tutup, tunggu siklus berikutnya.
3. **Guard berita** — jika ada event high-impact dalam `ff_within_hours`, bot **menahan entry**.
4. **Ambil data** — `copy_rates_from_pos()` untuk timeframe trading & higher TF.
5. **Indikator** — dihitung dari library `ta` + logika kustom.
6. **AI Ensemble** — tiap provider mengembalikan sinyal + confidence; diambil konsensus.
7. **Sinyal final** — gabungan skor TA, AI, dan filter berita. Di bawah threshold → WAIT.
8. **Hitung SL/TP** — entry dari **harga tick live** (`ask`/`bid`), ukuran SL dari `max_loss_per_trade_usd ÷ nilai per-point`, TP dari `reward_ratio`.
9. **Eksekusi** — `order_send()` via REST API.
10. **Sinkronisasi tampilan** — panel "HASIL ANALISIS" diselaraskan dengan harga & level order nyata (mencegah SL/TP analisa ≠ SL/TP order).
11. **Monitoring** — thread terpisah memantau Break-Even, Trailing Stop, dan Software TP/SL.

---

## 🏗️ Arsitektur & Struktur Proyek

```
dash/
├── main.cp314-win_amd64.pyd                      # Entry point + menu interaktif (Rich UI)
├── config.json                                   # Semua parameter bot (profil aktif)
├── .env                                          # Kredensial & API key 
├── requirements.txt                              # Dependensi Python
├── app.py                                        # Running bot
│
├── api/
│   └── mt5_api_client.cp314-win_amd64.pyd        # Klien REST ke gateway MT5 (mtapi.io)
│
├── bot/
│   └── bot_manager.cp314-win_amd64.pyd           # Otak loop trading otomatis (TradingBot)
│
├── trading/
│   └── trade_manager.cp314-win_amd64.pyd         # Eksekusi order, SL/TP, BEP, trailing
│
├── analyzers/
│   ├── market_analyzer.cp314-win_amd64.pyd       # Analisa TA + AI ensemble → sinyal
│   └── backtester.cp314-win_amd64.pyd            # Uji strategi pada data historis
│
├── risk/
│   ├── position_sizer.cp314-win_amd64.pyd        # Hitung lot & ukuran posisi
│   └── exposure_coach.cp314-win_amd64.pyd        # Saran eksposur risiko portofolio
│
├── journal/
│   ├── trade_journal.cp314-win_amd64.pyd         # Catatan order
│   └── analysis_log.cp314-win_amd64.pyd          # Catatan keputusan analisa
│
└── utils/
    ├── dashboard.cp314-win_amd64.pyd             # Kumpulkan data + render dashboard
    ├── dashboard_server.cp314-win_amd64.pyd      # HTTP server realtime (+ mode background)
    ├── dashboard_realtime.html                   # Halaman web realtime (auto-refresh)
    ├── dashboard_template.html                   # Template snapshot HTML
    ├── forex_factory.cp314-win_amd64.pyd         # Kalender ekonomi + market analysis
    ├── web_market.cp314-win_amd64.pyd            # Data market via Yahoo Finance
    ├── notifications.cp314-win_amd64.pyd         # Notifikasi Telegram
    ├── rich_display.cp314-win_amd64.pyd          # Komponen UI terminal (Rich)
    ├── license.cp314-win_amd64.pyd               # Verifikasi lisensi
    ├── api_symbol_detector.cp314-win_amd64.pyd   # Deteksi simbol broker yang benar
    └── news_test.cp314-win_amd64.pyd             # Utilitas uji query berita
```

---

## 📦 Instalasi

### Prasyarat
- **Python 3.14.4**
- Akun broker MetaTrader 5 (login, password, nama server)
- Koneksi internet

### Langkah

```bash
# 1. Clone repository
git clone https://github.com/ipkzone/TradingBot.git
cd TradingBot

# 2. (Disarankan) buat virtual environment
python -m venv venv
# Windows:
venv\Scripts\activate
# Linux/Mac:
source venv/bin/activate

# 3. Install dependensi
pip install -r requirements.txt

# 4. Salin & isi file .env (lihat bagian Konfigurasi)
copy .env.example .env      # Windows
# cp .env.example .env      # Linux/Mac

# 5. Jalankan bot
python app.py
```

---

## 🔧 Konfigurasi

### A. File `.env`

```ini
# --- KONEKSI MT5 (WAJIB) ---
MT5_LOGIN=12345678
MT5_PASSWORD=PasswordBrokerAnda
MT5_SERVER_NAME=Exness-MT5Trial7

# --- AI PROVIDERS (isi yang dipakai saja) ---
MIMO_API_KEY=...
MIMO_MODEL=...
QWEN_API_KEY=...
QWEN_MODEL=...
QWEN_BASE_URL=...
HEBCODE_API_KEY=...
HEBCODE_MODEL=...
HEBCODE_BASE_URL=...
GEMINI_API_KEY=...
DEEPSEEK_API_KEY=...
OPENROUTER_API_KEY=...
ANTHROPIC_BASE_URL=...
ANTHROPIC_API_KEY=...
ANTHROPIC_MODEL=...

# --- BERITA ---
NEWS_API_KEY=...

# --- NOTIFIKASI TELEGRAM (opsional) ---
TELEGRAM_BOT_TOKEN=...
TELEGRAM_CHAT_ID=...

# --- LISENSI ---
LICENSE_KEY=...
```

### B. File `config.json` (Parameter Bot)

| Grup | Kunci | Arti |
|---|---|---|
| **bot_settings** | `symbol_base` / `symbol_broker` | Simbol yang ditradingkan |
| | `timeframe` | Timeframe trading (mis. `H1`) |
| | `auto_trade` | Aktifkan eksekusi order otomatis |
| | `run_interval_seconds` | Jeda antar siklus loop |
| | `data_fetch_count` | Jumlah candle yang diunduh |
| **ai_settings** | `ai_provider` | `ensemble` atau nama provider |
| | `signal_strength_threshold` | Ambang minimum confidence untuk entry |
| | `ensemble_providers` | Daftar AI yang ikut voting |
| | `custom_rumus` | Prompt/strategi kustom |
| **trade_parameters** | `lot_size`, `slippage` | Ukuran lot & toleransi slippage |
| | `max_total_positions` | Batas total posisi terbuka |
| **risk_management** | `max_loss_per_trade_usd` | Risiko nominal per posisi |
| | `reward_ratio` | Rasio TP terhadap SL (mis. 2.5) |
| | `atr_sl_multiplier` | Pengali ATR untuk SL |
| | `conversion_rate_usd_to_idr` | Kurs untuk akun IDR |
| **position_management** | `enable_bep` | Aktifkan Break-Even |
| | `enable_trailing_sl` | Aktifkan Trailing Stop |
| | `software_tp_sl_enabled` | Bot kelola TP/SL sendiri |
| **news_settings** | `enable_news_filter` | Aktifkan filter berita |
| | `ff_within_hours` | Jendela proteksi high-impact (jam) |
| | `ff_feed_url` | Feed kalender Forex Factory |
| **ta_parameters** | `adx_filter_level`, dll | Parameter indikator teknikal |
| **dashboard_settings** | `auto_launch_on_trade` | Buka dashboard otomatis saat trading |
| | `port` | Port server dashboard (default 8787) |
| | `refresh_interval_ms` | Interval auto-refresh dashboard |
| **market_settings** | `source` | `web` (Yahoo) atau `mt5` |

---

## 🚀 Menjalankan Bot & Daftar Menu

```bash
python main.py
```

Setelah login berhasil, akan tampil **Main Menu**:

| Menu | Fungsi |
|------|--------|
| **[01]** | **Start Trading (Auto - TA)** — loop otomatis penuh + dashboard background |
| **[02]** | **Start Trading + Rumus AI** — auto dengan prompt/strategi kustom |
| **[03]** | **Start Trading Manual** — BUY/SELL manual |
| **[04]** | **Checking Signal Market** — lihat analisa AI tanpa entry |
| **[05]** | **Checking Pasar** — status Open/Close semua market |
| **[06]** | **View Posisi Terbuka** — daftar posisi + harga & jarak ke SL/TP |
| **[07]** | **Close Posisi** — tutup semua / pilih posisi |
| **[08]** | **Checking Profit & Lose** — ringkasan P/L floating + realized |
| **[09]** | **Checking Forex Factory** — kalender ekonomi + market analysis |
| **[10]** | **View Configuration** — lihat seluruh setting |
| **[11]** | **Edit Settings** — ubah parameter per-tombol (termasuk **Change Symbol**) |
| **[12]** | **Apply Smart Preset** — terapkan profil siap-pakai |
| **[13]** | **Run Backtest** — uji strategi pada data historis |
| **[14]** | **Dashboard Monitoring** — snapshot terminal/HTML |
| **[15]** | **Dashboard Realtime** — dashboard web auto-refresh (manual) |
| **[0]** | **Exit** |

### Sub-menu Edit Settings `[11]`
```
[1] Bot Settings        [5] Position Management
[2] AI Settings         [6] TA Parameters
[3] Trade Parameters    [7] API Settings
[4] Risk Management      [8] Change Symbol (base & broker) ⭐
[0] Kembali
```

---


## 🛡️ Manajemen Risiko

Bot mengutamakan **proteksi modal**:

1. **Risiko nominal tetap** — Anda menentukan kerugian maksimum per posisi dalam USD. Ukuran SL dihitung mundur dari angka ini, sehingga **kerugian terkendali** apa pun instrumennya.
2. **Reward Ratio** — TP otomatis pada kelipatan SL (mis. 1:2.5).
3. **Break-Even** — setelah profit `bep_min_profit_usd`, SL dipindah ke titik impas → posisi "bebas risiko".
4. **Trailing Stop** — setelah profit `trailing_start_profit_usd`, SL mengikuti harga untuk mengunci keuntungan.
5. **Software TP/SL** — bot menutup posisi sendiri, tidak semata bergantung server broker.
6. **Filter berita** — hindari volatilitas ekstrem saat rilis data high-impact.
7. **Batas posisi** — cegah over-exposure.

> 💡 Untuk akun **IDR**, semua nominal dikonversi otomatis memakai `conversion_rate_usd_to_idr`.

---

## 📺 Dashboard

### Dashboard Realtime (Web)
- Akses otomatis di `http://127.0.0.1:8787/` (port bisa diubah).
- Auto-refresh tiap beberapa detik tanpa reload.
- Menampilkan: **akun & P/L, posisi terbuka (dengan jarak ke SL/TP), analisa AI terkini, kalender Forex Factory, statistik dampak berita, performa & chart harga market**.
- ⭐ **Otomatis terbuka di belakang layar** saat Start Trading `[01]`/`[02]`, dan **tertutup otomatis** saat trading berhenti. Diatur via `dashboard_settings.auto_launch_on_trade`.

### Dashboard Snapshot
- Menu `[14]`: ringkasan cepat di terminal atau ekspor HTML statis.

---

## ❓ FAQ & Troubleshooting

**Q: Login gagal / `Not connected`?**
Pastikan `MT5_LOGIN`, `MT5_PASSWORD`, dan `MT5_SERVER_NAME` benar. Nama server demo Exness sering berganti (Trial7/8/9). Uji dengan `python test_login.py`.

**Q: Chart market / Performance kosong?**
Pastikan `market_settings.source = "web"` dan simbol dikenali. Pemetaan Yahoo sudah mendukung emas (`GC=F`), crypto, indeks, dan FX.

**Q: Forex Factory "tidak ada event"?**
Tabel dashboard menampilkan semua event minggu berjalan; bila feed kosong di akhir pekan, ditampilkan event terbaru sebagai fallback.

**Q: AI selalu WAIT?**
Turunkan `signal_strength_threshold`, atau cek apakah ada berita high-impact yang menahan entry. Periksa juga API key AI di `.env`.

**Q: Ganti pair trading?**
Menu `[11] Edit Settings → [8] Change Symbol`, lalu ketik simbol baru (mis. `GBPUSDm`). Restart sesi trading bila sedang berjalan.

---

## ⚠️ Disclaimer

> Perangkat lunak ini disediakan **"sebagaimana adanya"** untuk tujuan edukasi dan riset. Trading forex/CFD/crypto mengandung **risiko tinggi** dan dapat menyebabkan **kehilangan seluruh modal**. Kinerja masa lalu tidak menjamin hasil masa depan. **Gunakan akun demo terlebih dahulu.** Penulis tidak bertanggung jawab atas kerugian finansial apa pun. Gunakan dengan risiko Anda sendiri.

---

<div align="center">

**TRADING BOT v8.0.2** · Developed by **Iddant ID**

*Kecerdasan Buatan untuk Keuntungan Nyata* 🚀

</div>
