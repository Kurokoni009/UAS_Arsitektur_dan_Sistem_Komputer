# 🕐 8-bit Digital Stopwatch (HH:MM:SS)

> **Ujian Akhir Semester — Arsitektur dan Sistem Komputer**  
> Prodi S1 Kecerdasan Artifisial, FMIPA — Universitas Negeri Surabaya  
> Tahun Akademik 2025/2026

---

## 👥 Penyusun Proyek

| Nama | NIM |
|------|-----|
| Narendra Farel Arivanto | 25032014021 |
| Nadhif Dafa Aditra | 25032014029 |
| Fahmi Bima Yudhistira | 25032014011 |

---

## 📋 Deskripsi Proyek

Proyek ini mengimplementasikan **Stopwatch Digital 8-bit** menggunakan simulator **Logisim Evolution**. Stopwatch dirancang sepenuhnya dengan komponen digital logic dasar seperti flip-flop, gerbang logika, counter BCD, dan decoder 7-segment — tanpa menggunakan modul counter bawaan Logisim.

### Fitur Utama

- ⏱️ **Display HH:MM:SS** — Menampilkan jam, menit, dan detik secara bersamaan via 6 digit 7-segment display
- ▶️ **Start/Stop** — Toggle counting dengan satu tombol; stopwatch berhenti di waktu terakhir
- 🏗️ **Full Custom Logic** — Semua komponen dibangun dari gate dasar: AND, OR, NOT, XOR, dan D Flip-Flop
- 📊 **Modular Design** — Setiap sub-circuit (BCD Counter, 7-Seg Decoder, Control Unit, Clock Divider) dibuat sebagai modul terpisah yang dapat diuji secara independen

### Komponen Digital yang Digunakan

| Komponen | Fungsi |
|----------|--------|
| D Flip-Flop × 24 | Menyimpan state setiap bit counter (4-bit × 6 counter) |
| AND Gate | Carry logic, enable gating |
| OR Gate | Kombinasi kondisi reset dan carry |
| NOT Gate | Inversi sinyal kontrol |
| XOR Gate | Adder partial sum |
| 7-Segment Decoder × 6 | Konversi BCD ke tampilan segmen |
| SR Latch | Control unit Start/Stop |

---

## 🏛️ Arsitektur Sistem

```
                    ┌─────────────┐
     CLK ──────────▶│ Clock Divider│──── 1Hz ──────────────────────────┐
                    └─────────────┘                                     │
                                                                        │
  START ──┐    ┌──────────────────┐                                     │
          ├───▶│   Control Unit   │──── EN (counting_enable) ──────────┐│
  STOP  ──┘    │   (SR Latch)     │──── CLR (async reset)  ────────────┤│
               └──────────────────┘                                    ││
                                                                       ││
               ┌──────────────────────────────────────────────────┐   ││
               │                BCD Counter Chain                  │◀──┘│
               │                                                   │    │
               │  ┌─────────┐ carry ┌─────────┐ carry ┌─────────┐│    │
               │  │SEC_ONES │──────▶│SEC_TENS │──────▶│MIN_ONES ││◀───┘
               │  │  (0–9)  │       │  (0–5)  │       │  (0–9)  ││
               │  └────┬────┘       └────┬────┘       └────┬────┘│
               │       │                 │                  │     │
               │  ┌────▼────┐       ┌────▼────┐       ┌────▼────┐│
               │  │7-SEG DEC│       │7-SEG DEC│       │7-SEG DEC││
               │  └────┬────┘       └────┬────┘       └────┬────┘│
               │      SS               SS                  MM    │
               │                                                   │
               │  ┌─────────┐ carry ┌─────────┐ carry ┌─────────┐│
               │  │MIN_TENS │──────▶│ HR_ONES │──────▶│ HR_TENS ││
               │  │  (0–5)  │       │  (0–9)  │       │  (0–9)  ││
               │  └────┬────┘       └────┬────┘       └────┬────┘│
               │       │                 │                  │     │
               │  ┌────▼────┐       ┌────▼────┐       ┌────▼────┐│
               │  │7-SEG DEC│       │7-SEG DEC│       │7-SEG DEC││
               │  └────┬────┘       └────┬────┘       └────┬────┘│
               │      MM               HH                  HH    │
               └──────────────────────────────────────────────────┘

                        ┌─────────────────────────────┐
                        │    DISPLAY: HH : MM : SS     │
                        │   [  ] [  ] : [  ] [  ] : [  ] [  ]  │
                        └─────────────────────────────┘
```

### State Machine (Control Unit)

```
         RESET
           │
           ▼
  ┌─────────────────┐   START pressed   ┌─────────────────┐
  │      IDLE       │──────────────────▶│    RUNNING      │
  │  (EN = 0)       │                   │   (EN = 1)      │
  │  Counter holds  │◀──────────────────│  Counter counts │
  └─────────────────┘   START pressed   └─────────────────┘
           ▲                                     │
           └─────────────── RESET ───────────────┘
```

---

## 🚀 Cara Menjalankan / Simulasi

### Prerequisites

- **Logisim Evolution** versi 3.8.0 atau lebih baru
- Java Runtime Environment (JRE) 11+

### Download Logisim Evolution

```bash
# Download dari GitHub releases resmi
https://github.com/logisim-evolution/logisim-evolution/releases/latest
```

Atau download file `.jar` langsung dan jalankan:
```bash
java -jar logisim-evolution.jar
```

### Langkah Simulasi

1. **Buka file circuit**
   ```
   File → Open → pilih file: src/stopwatch.circ
   ```

2. **Verifikasi tampilan**
   - Pastikan 6 display 7-segment terlihat dan menunjukkan `00:00:00`
   - Pastikan ada 2 tombol: **START** dan **STOP**

3. **Set kecepatan clock**
   - Klik kanan komponen Clock → pilih **Edit Properties**
   - Set **High Duration** dan **Low Duration** ke `500ms` (= 1Hz clock)
   
   > Atau untuk demo yang lebih cepat, set ke `50ms` (10Hz) — stopwatch akan berjalan 10× lebih cepat

4. **Jalankan simulasi**
   ```
   Simulate → Run Simulation  (atau tekan Ctrl+E)
   ```

5. **Operasikan stopwatch**
   | Aksi | Cara |
   |------|------|
   | Mulai hitung | Klik tombol **START** |
   | Pause / berhenti | Klik tombol **START** lagi |
   | Reset ke 00:00:00 | Klik tombol **RESET** |

6. **Verifikasi carry chain**
   - Untuk test cepat: set clock ke 512Hz dan amati detik berganti setiap 0.5 detik simulasi
   - Pastikan `SS: 59 → 00` dan `MM` bertambah 1 dengan benar
   - Pastikan `MM: 59 → 00` dan `HH` bertambah 1 dengan benar

---

## 📁 Struktur Repository

```
stopwatch-digital/
│
├── src/
│   └── stopwatch.circ          # File circuit utama Logisim Evolution
│
├── docs/
│   ├── laporan.md              # Laporan singkat proyek
│   ├── architecture.md         # Penjelasan arsitektur detail
│   ├── flowchart.png           # Flowchart sistem
│   ├── diagram_blok.png        # Block diagram
│   └── reconstruction.md       # Panduan rebuild circuit dari nol
│
├── test/
│   ├── test_results.md         # Hasil pengujian lengkap
│   ├── test_cases.md           # Daftar test case
│   └── screenshots/            # Screenshot simulasi berjalan
│       ├── init_state.png
│       ├── running_state.png
│       ├── pause_state.png
│       ├── reset_state.png
│       └── carry_transition.png
│
└── README.md
```

---

## 🎬 Video Demo

> 📹 **[Link Video YouTube — klik di sini](_LINK_YOUTUBE_)**

**Isi video:**
- Perkenalan singkat anggota tim dan topik proyek
- Penjelasan arsitektur: clock divider, BCD counter chain, 7-segment decoder, control unit
- Demo live simulasi di Logisim Evolution (start, stop, reset, carry chain)
- Penjelasan cara kerja masing-masing sub-circuit
- Output terukur: timing counter, verifikasi carry pada 00:59:59 → 01:00:00
- Kesimpulan dan kendala yang ditemui

---

## 📚 Referensi

- Mano, M. M., & Ciletti, M. D. (2013). *Digital Design* (5th ed.). Pearson.
- Logisim Evolution Documentation: https://github.com/logisim-evolution/logisim-evolution
- Floyd, T. L. (2014). *Digital Fundamentals* (11th ed.). Pearson.

