# Digital Logic Design: Stopwatch Digital HH:MM:SS di Logisim Evolution

> Proyek Ujian Akhir Semester Genap 2025/2026  
> Mata kuliah: Arsitektur dan Sistem Komputer  
> Program studi: S1 Kecerdasan Artifisial, FMIPA Universitas Negeri Surabaya

## Penyusun Proyek

| Nama | NIM |
|---|---|
| Narendra Farel Arivanto | [25032014021] |
| Nadhif Dafa Aditra | [25032014029] |
| Fahmi Bima Yudhistira | [25032014011] |

## Deskripsi Singkat Proyek

Proyek ini memilih topik **Digital Logic Design** dengan bentuk **embedded system dalam simulator**. Sistem yang dibuat adalah stopwatch digital `HH:MM:SS` berbasis Logisim Evolution. Rancangan memakai enam counter BCD, yaitu `SEC_ONES`, `SEC_TENS`, `MIN_ONES`, `MIN_TENS`, `HR_ONES`, dan `HR_TENS`. Setiap digit mengeluarkan nilai `Q[3:0]` ke display 7-segment mode BCD.

Kontrol utama berada di top-level. Tombol `START` mengaktifkan flip-flop enable (`EN_FF`), tombol `STOP` menonaktifkan `EN_FF`, dan tombol `RESET` mengirim sinyal `CLR` global ke seluruh counter serta mengembalikan sistem ke `00:00:00`. Rantai `CARRY` dipakai agar digit berikutnya bertambah hanya ketika digit sebelumnya mencapai nilai terminal.

## Fitur Utama

| Fitur | Penjelasan |
|---|---|
| Display `HH:MM:SS` | Enam digit BCD ditampilkan melalui enam 7-segment display. |
| START | Mengubah `EN_FF` menjadi aktif sehingga stopwatch mulai menghitung. |
| STOP | Menahan nilai terakhir dengan membuat `EN=0`. |
| RESET | Menghapus seluruh counter melalui `CLR` dan menghentikan stopwatch. |
| Carry chain | Mengatur transisi `00:00:59 → 00:01:00` dan `00:59:59 → 01:00:00`. |
| Modular counter | Menggunakan sub-circuit `BCD_0to9` dan `BCD_0to5`. |

## Struktur Repository

```text
stopwatch_uas/
├── README.md
├── src/
│   ├── stopwatch.circ                 
│   └── README_src.md                   
├── docs/
│   ├── laporan Project.docx
│   ├── architecture.txt
│   ├── input_output_system.txt
│   ├── diagram_blok.png
│   ├── flowchart.png
│   └── screenshots/
│       └── main_circuit.png
│       ├── BCD_0to5_circuit.png
│       └── BCD_0to9_circuit.png
└── test/
    ├── test_results.md
    └── test_cases.md
```

## Cara Menjalankan Simulasi

1. Unduh dan buka **Logisim Evolution** versi 4.1.0 atau versi yang kompatibel.
2. Buka file `src/stopwatch.circ` melalui menu `File → Open`.
3. Pastikan top-level circuit menampilkan tombol `START`, `STOP`, `RESET`, input `CLK_1KHz`, enam counter BCD, dan enam display.
4. Aktifkan simulasi melalui `Simulate → Simulation Enabled`.
5. Aktifkan clock melalui `Simulate → Ticks Enabled` dan atur `Tick Frequency ke 1KHz`.
6. Tekan `START` untuk menjalankan stopwatch.
7. Tekan `STOP` untuk menghentikan hitungan pada nilai terakhir.
8. Tekan `START` kembali untuk melanjutkan hitungan.
9. Tekan `RESET` untuk mengembalikan tampilan ke `00:00:00` dan membuat `EN=1`.

## Output yang Diharapkan

| Kondisi | Output yang Seharusnya Muncul |
|---|---|
| Power on | `00:00:00`, stopwatch diam. |
| START ditekan | Counter bertambah setiap pulsa `CLK_1Hz`. |
| STOP ditekan | Nilai display berhenti pada waktu terakhir. |
| START setelah STOP | Counter lanjut dari nilai terakhir. |
| RESET ditekan | Semua digit kembali ke `00:00:00`, `EN=0`. |
| `00:00:59 + 1 detik` | Display berubah menjadi `00:01:00`. |
| `00:59:59 + 1 detik` | Display berubah menjadi `01:00:00`. |

## Link Video Demo

Link YouTube: `https://youtu.be/Iv264pw0W20`

Video demo disiapkan berdurasi **10 sampai 15 menit** dan memuat: perkenalan anggota, topik dan tujuan proyek, demo sistem live, penjelasan cara kerja rangkaian, output terukur dari pengujian, kesimpulan, dan kendala.

## Link GitHub

Link repository GitHub public: `https://github.com/Kurokoni009/UAS_Arsitektur_dan_Sistem_Komputer`

## Catatan Kejujuran Teknis

Proyek ini lebih tepat disebut **embedded system digital berbasis counter BCD**, bukan komputer 8-bit lengkap. Sistem tidak memiliki ALU umum, register file, memori program, atau instruction set seperti komputer 8-bit. Namun, sistem tetap memenuhi jalur Digital Logic Design karena dibangun dari logika sekuensial, flip-flop, counter, sinyal kontrol, bus `Q[3:0]`, dan I/O digital di Logisim.
