# Arsitektur Sistem Stopwatch Digital HH:MM:SS

## 1. Posisi Proyek dalam Topik Digital Logic Design

Proyek ini berada pada jalur **Digital Logic Design** sebagai embedded system dalam simulator. Rangkaian tidak dirancang sebagai komputer 8-bit umum, tetapi sebagai sistem digital khusus yang menjalankan satu fungsi: menghitung waktu dalam format `HH:MM:SS`. Karena itu, arsitekturnya dijelaskan melalui blok kontrol, register keadaan, counter BCD, bus sinyal, dan I/O digital.

## 2. Hierarki Sistem Aktual

```text
stopwatch.circ
│
├── Top-Level Circuit
│   ├── Input: CLK_1Hz
│   ├── Input: START
│   ├── Input: STOP
│   ├── Input: RESET
│   ├── Kontrol: EN_FF
│   ├── Tunnel: EN
│   ├── Tunnel: CLR
│   ├── Counter: SEC_ONES  menggunakan BCD_0to9
│   ├── Counter: SEC_TENS  menggunakan BCD_0to5
│   ├── Counter: MIN_ONES  menggunakan BCD_0to9
│   ├── Counter: MIN_TENS  menggunakan BCD_0to5
│   ├── Counter: HR_ONES   menggunakan BCD_0to9
│   ├── Counter: HR_TENS   menggunakan BCD_0to9
│   └── Output: enam 7-segment BCD display
│
├── Sub-circuit BCD_0to9
│   ├── Input: CLR, EN, CLK
│   ├── Output: Q[3:0], CARRY
│   └── Fungsi: menghitung 0 sampai 9 lalu kembali ke 0
│
└── Sub-circuit BCD_0to5
    ├── Input: CLR, EN, CLK
    ├── Output: Q[3:0], CARRY
    └── Fungsi: menghitung 0 sampai 5 lalu kembali ke 0
```

## 3. Pemetaan Arsitektur terhadap Rubrik Digital Logic

Rubrik Digital Logic Design meminta penjelasan arsitektur sistem seperti control unit, register, memory, ALU, dan I/O bus. Pada stopwatch ini, padanannya adalah sebagai berikut.

| Elemen Arsitektur | Penerapan pada Stopwatch | Alasan Teknis |
|---|---|---|
| Control unit | `EN_FF`, `START`, `STOP`, `RESET`, `CLR` | Mengatur kapan sistem berjalan, berhenti, dan kembali ke nol. |
| Register/state memory | Flip-flop internal pada setiap counter dan `EN_FF` | Menyimpan nilai digit waktu dan status running/stopped. |
| Arithmetic logic | Incrementer/counter internal pada `BCD_0to9` dan `BCD_0to5` | Melakukan operasi tambah satu pada setiap pulsa clock saat enable aktif. |
| Memory | State counter `Q[3:0]` pada enam digit | Sistem menyimpan keadaan waktu, bukan memori program. |
| I/O bus | `CLK_1Hz`, `EN`, `CLR`, `Q[3:0]`, `CARRY` | Mengalirkan sinyal antarblok dan dari counter ke display. |
| Output interface | Enam display 7-segment BCD | Menampilkan nilai waktu kepada pengguna. |

Dengan pemetaan ini, penjelasan arsitektur tetap mengikuti tuntutan penilaian, tetapi tidak memaksakan stopwatch sebagai komputer 8-bit lengkap.

## 4. Diagram Blok

Lihat file `docs/diagram_blok.png` untuk diagram visual. Versi teksnya sebagai berikut.

```text
START ─┐
STOP  ─┼──> EN_FF ── EN ──> SEC_ONES ─CARRY─> SEC_TENS ─CARRY─> MIN_ONES
RESET ─┘       │              │                       │                    │
               └── CLR ──────┴───────────────────────┴────────────────────┘

MIN_ONES ─CARRY─> MIN_TENS ─CARRY─> HR_ONES ─CARRY─> HR_TENS
   │                  │                 │                │
   ▼                  ▼                 ▼                ▼
Display            Display           Display          Display
```

## 5. Wiring Carry Chain

```text
EN_FF.Q          → SEC_ONES.EN
SEC_ONES.CARRY  → SEC_TENS.EN
SEC_TENS.CARRY  → MIN_ONES.EN
MIN_ONES.CARRY  → MIN_TENS.EN
MIN_TENS.CARRY  → HR_ONES.EN
HR_ONES.CARRY   → HR_TENS.EN
```

Sinyal `CLR` dari tombol `RESET` dikirim ke seluruh counter. Jika reset juga dihubungkan ke clear `EN_FF`, sistem akan kembali ke `00:00:00` dan berhenti.

## 6. Mekanisme Counter BCD

Counter `BCD_0to9` digunakan untuk digit yang harus menghitung 0 sampai 9. Counter ini aktif ketika `EN=1`, menerima pulsa clock, lalu menaikkan nilai `Q[3:0]`. Ketika mencapai nilai terminal 9, counter menghasilkan `CARRY` untuk mengaktifkan digit berikutnya dan kembali ke 0 pada siklus berikutnya.

Counter `BCD_0to5` digunakan untuk puluhan detik dan puluhan menit. Counter ini hanya menghitung 0 sampai 5 agar format waktu tetap valid. Ketika mencapai nilai terminal 5, sinyal `CARRY` mengaktifkan digit berikutnya.

## 7. Keputusan Desain

| Keputusan | Alasan |
|---|---|
| Memakai counter BCD | Nilai langsung sesuai digit desimal pada display. |
| Memakai dua jenis counter, `0–9` dan `0–5` | Puluhan detik dan puluhan menit hanya valid sampai 5. |
| Memakai carry ripple | Sederhana dan cukup stabil untuk clock 1 Hz. |
| Memakai tunnel `EN` dan `CLR` | Mengurangi kabel silang dan membuat top-level lebih terbaca. |
| Memakai display BCD langsung | Struktur lebih sederhana karena tidak perlu sub-circuit decoder eksternal. |
| Reset dibuat global | Semua digit kembali ke nol secara serentak. |

## 8. Keterbatasan Arsitektur

Sistem ini bukan real-time clock 24 jam karena digit jam dirancang sampai `99`. Sistem juga masih menggunakan ripple carry, sehingga pada simulasi cepat dapat muncul glitch singkat saat transisi berantai. Keterbatasan ini tidak mengganggu fungsi dasar stopwatch pada clock 1 Hz, tetapi tetap perlu disebutkan saat presentasi karena menjadi bagian dari evaluasi kritis.
