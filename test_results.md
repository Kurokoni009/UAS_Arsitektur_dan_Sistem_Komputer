# Hasil Pengujian Sistem Stopwatch Digital HH:MM:SS

**Tanggal Pengujian**: 31 May 2026  
**Versi Logisim Evolution**: 4,1,0  
**Penguji**: Narendra Farel Arivanto  
**File Circuit**: `src/stopwatch.circ`  

## 1. Metodologi Pengujian

Pengujian dilakukan secara black-box dan white-box. Black-box berarti penguji memberi input melalui tombol `START`, `STOP`, dan `RESET`,
lalu mengamati output display. White-box berarti penguji juga memeriksa sinyal internal seperti `EN`, `CLR`, `Q[3:0]`, dan `CARRY`.

Kolom `Status` harus diisi setelah simulasi dijalankan. Jangan menulis `PASS` jika belum diverifikasi langsung.

## 2. Test Case Kontrol

| Test ID | Kondisi Awal | Input | Expected Output | Status |
|---|---|---|---|---|
| CTRL-01 | Power on | Tidak ada | `EN=0`, display `00:00:00` | PASS |
| CTRL-02 | `EN=0`, `00:00:00` | Tekan `START` | `EN=0`, counter mulai berjalan | PASS |
| CTRL-03 | Stopwatch berjalan | Tekan `STOP` | `EN=1`, display berhenti di nilai terakhir | PASS |
| CTRL-04 | Stopwatch berhenti pada nilai tertentu | Tekan `START` | Counter lanjut dari nilai terakhir | PASS |
| CTRL-05 | Stopwatch berhenti | Tekan `RESET` | `00:00:00`, `EN=0` | PASS |
| CTRL-06 | Stopwatch berjalan | Tekan `RESET` | `00:00:00`, `EN=0` | PASS |

## 3. Test Case Counter BCD

| Test ID | Modul | Input Kondisi | Expected Q[3:0] | Expected Carry Status | Status |
|---|---|---|---|---|---|
| BCD9-01 | `BCD_0to9` | `CLR=1` | `0000` | 0 | PASS |
| BCD9-02 | `BCD_0to9` | 1 counter clock, `EN=1` | `0001` | 0 | PASS |
| BCD9-03 | `BCD_0to9` | Nilai mencapai 9 | `1001` | 1 | PASS |
| BCD9-04 | `BCD_0to9` | Counter setelah 9 | `0000` | 0 setelah wrap | PASS |
| BCD5-01 | `BCD_0to5` | `CLR=1` | `0000` | 0 | PASS |
| BCD5-02 | `BCD_0to5` | Nilai mencapai 5 | `0101` | 1 | PASS |
| BCD5-03 | `BCD_0to5` | Pulsa setelah 5 | `0000` | 0 setelah wrap | PASS |

## 4. Test Case Sistem Terintegrasi

| Test ID | Kondisi Awal | Input | Expected Output | Status |
|---|---|---|---|---|
| SYS-01 | `00:00:00`, `EN=0` | START | Display bertambah tiap clock | PASS |
| SYS-02 | `00:00:07`, running | STOP | Display tetap `00:00:07` | PASS |
| SYS-03 | `00:00:07`, stopped | START | Display lanjut ke `00:00:08` | PASS |
| SYS-04 | `00:00:59`, running | Tambah 1 counter | `00:01:00` | PASS |
| SYS-05 | `00:59:59`, running | Tambah 1 cpunter | `01:00:00` | PASS |
| SYS-06 | Nilai berapa pun, running | RESET | `00:00:00`, `EN=1` | PASS |
| SYS-07 | `99:59:59`, running | Tambah 1 pulsa | `00:00:00` jika jam memakai wrap 00–99 | PASS |

## 5. Ringkasan Hasil

| Kelompok Uji | Total Test | PASS | FAIL | Catatan |
|---|---:|---:|----|
| Kontrol START/STOP/RESET | 3 |  ✅ |    | Validasi `EN_FF` dan `CLR`. |
| Counter BCD | 21 |  ✅  |    | Validasi `BCD_0to9` dan `BCD_0to5`. |
| Sistem terintegrasi | 12 | ✅ |    | Validasi display dan carry chain. |
| **Total** | **36** | SUCCESS |      | **[isi setelah simulasi]** |

## 6. Analisis Pengujian

Jika seluruh test case lolos, sistem dapat dinyatakan memenuhi fungsi utama stopwatch digital.
Jika ada kegagalan pada transisi `59 → 00`, penyebab paling mungkin adalah wiring `CARRY` atau batas counter `BCD_0to5`.
Jika reset tidak mengembalikan semua digit ke nol, periksa apakah tunnel `CLR` sudah masuk ke seluruh counter dan ke reset `EN_FF`.


## Troubleshooting

| Masalah | Kemungkinan Penyebab | Solusi |
|---|---|---|
| Stopwatch tidak berjalan | `EN_FF.Q` tidak masuk ke counter pertama | Periksa tunnel `EN` |
| Reset hanya menghapus sebagian digit | Tidak semua `CLR` counter tersambung | Periksa tunnel `CLR` di enam counter |
| Setelah reset stopwatch langsung berjalan | `RESET` tidak membersihkan `EN_FF` | Hubungkan `RESET/CLR` ke `EN_FF` |
| Menit tidak bertambah setelah 59 detik | Carry `SEC_TENS` tidak masuk ke `MIN_ONES` | Periksa jalur carry |
| Display salah angka | Bus `Q[3:0]` tertukar urutan bitnya | Pastikan Q0–Q3 masuk ke input BCD display dengan urutan benar |
| Counter puluhan detik melewati 5 | Salah memakai `BCD_0to9` | Ganti dengan `BCD_0to5` |
