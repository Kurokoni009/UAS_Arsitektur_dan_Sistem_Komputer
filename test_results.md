# Hasil Pengujian Sistem Stopwatch Digital HH:MM:SS

**Tanggal Pengujian**: [isi tanggal]  
**Versi Logisim Evolution**: [isi versi]  
**Penguji**: [isi nama anggota]  
**File Circuit**: `src/stopwatch.circ`  

## 1. Metodologi Pengujian

Pengujian dilakukan secara black-box dan white-box. Black-box berarti penguji memberi input melalui tombol `START`, `STOP`, dan `RESET`,
lalu mengamati output display. White-box berarti penguji juga memeriksa sinyal internal seperti `EN`, `CLR`, `Q[3:0]`, dan `CARRY`.

Kolom `Actual` dan `Status` harus diisi setelah simulasi dijalankan. Jangan menulis `PASS` jika belum diverifikasi langsung.

## 2. Test Case Kontrol

| Test ID | Kondisi Awal | Input | Expected Output | Actual | Status |
|---|---|---|---|---|---|
| CTRL-01 | Power on | Tidak ada | `EN=0`, display `00:00:00` | [isi] | [PASS/FAIL] |
| CTRL-02 | `EN=0`, `00:00:00` | Tekan `START` | `EN=1`, counter mulai berjalan | [isi] | [PASS/FAIL] |
| CTRL-03 | Stopwatch berjalan | Tekan `STOP` | `EN=0`, display berhenti di nilai terakhir | [isi] | [PASS/FAIL] |
| CTRL-04 | Stopwatch berhenti pada nilai tertentu | Tekan `START` | Counter lanjut dari nilai terakhir | [isi] | [PASS/FAIL] |
| CTRL-05 | Stopwatch berhenti | Tekan `RESET` | `00:00:00`, `EN=0` | [isi] | [PASS/FAIL] |
| CTRL-06 | Stopwatch berjalan | Tekan `RESET` | `00:00:00`, `EN=0` | [isi] | [PASS/FAIL] |

## 3. Test Case Counter BCD

| Test ID | Modul | Input Kondisi | Expected Q[3:0] | Expected Carry | Actual | Status |
|---|---|---|---|---|---|---|
| BCD9-01 | `BCD_0to9` | `CLR=1` | `0000` | 0 | [isi] | [PASS/FAIL] |
| BCD9-02 | `BCD_0to9` | 1 pulsa clock, `EN=1` | `0001` | 0 | [isi] | [PASS/FAIL] |
| BCD9-03 | `BCD_0to9` | Nilai mencapai 9 | `1001` | 1 | [isi] | [PASS/FAIL] |
| BCD9-04 | `BCD_0to9` | Pulsa setelah 9 | `0000` | 0 setelah wrap | [isi] | [PASS/FAIL] |
| BCD5-01 | `BCD_0to5` | `CLR=1` | `0000` | 0 | [isi] | [PASS/FAIL] |
| BCD5-02 | `BCD_0to5` | Nilai mencapai 5 | `0101` | 1 | [isi] | [PASS/FAIL] |
| BCD5-03 | `BCD_0to5` | Pulsa setelah 5 | `0000` | 0 setelah wrap | [isi] | [PASS/FAIL] |

## 4. Test Case Sistem Terintegrasi

| Test ID | Kondisi Awal | Input | Expected Output | Actual | Status |
|---|---|---|---|---|---|
| SYS-01 | `00:00:00`, `EN=0` | START | Display bertambah tiap clock | [isi] | [PASS/FAIL] |
| SYS-02 | `00:00:07`, running | STOP | Display tetap `00:00:07` | [isi] | [PASS/FAIL] |
| SYS-03 | `00:00:07`, stopped | START | Display lanjut ke `00:00:08` | [isi] | [PASS/FAIL] |
| SYS-04 | `00:00:59`, running | Tambah 1 pulsa | `00:01:00` | [isi] | [PASS/FAIL] |
| SYS-05 | `00:59:59`, running | Tambah 1 pulsa | `01:00:00` | [isi] | [PASS/FAIL] |
| SYS-06 | Nilai berapa pun, running | RESET | `00:00:00`, `EN=0` | [isi] | [PASS/FAIL] |
| SYS-07 | `99:59:59`, running | Tambah 1 pulsa | `00:00:00` jika jam memakai wrap 00–99 | [isi] | [PASS/FAIL] |

## 5. Ringkasan Hasil

| Kelompok Uji | Total Test | PASS | FAIL | Catatan |
|---|---:|---:|---:|---|
| Kontrol START/STOP/RESET | 6 | [isi] | [isi] | Validasi `EN_FF` dan `CLR`. |
| Counter BCD | 7 | [isi] | [isi] | Validasi `BCD_0to9` dan `BCD_0to5`. |
| Sistem terintegrasi | 7 | [isi] | [isi] | Validasi display dan carry chain. |
| **Total** | **20** | **[isi]** | **[isi]** | **[isi setelah simulasi]** |

## 6. Analisis Pengujian

Jika seluruh test case lolos, sistem dapat dinyatakan memenuhi fungsi utama stopwatch digital.
Jika ada kegagalan pada transisi `59 → 00`, penyebab paling mungkin adalah wiring `CARRY` atau batas counter `BCD_0to5`.
Jika reset tidak mengembalikan semua digit ke nol, periksa apakah tunnel `CLR` sudah masuk ke seluruh counter dan ke reset `EN_FF`.

Data pengujian yang valid harus berasal dari simulasi langsung. Karena itu,
screenshot hasil uji sebaiknya ditambahkan ke folder `test/screenshots/` atau `docs/screenshots/`.

## Troubleshooting

| Masalah | Kemungkinan Penyebab | Solusi |
|---|---|---|
| Stopwatch tidak berjalan | `EN_FF.Q` tidak masuk ke counter pertama | Periksa tunnel `EN` |
| Reset hanya menghapus sebagian digit | Tidak semua `CLR` counter tersambung | Periksa tunnel `CLR` di enam counter |
| Setelah reset stopwatch langsung berjalan | `RESET` tidak membersihkan `EN_FF` | Hubungkan `RESET/CLR` ke `EN_FF` |
| Menit tidak bertambah setelah 59 detik | Carry `SEC_TENS` tidak masuk ke `MIN_ONES` | Periksa jalur carry |
| Display salah angka | Bus `Q[3:0]` tertukar urutan bitnya | Pastikan Q0–Q3 masuk ke input BCD display dengan urutan benar |
| Counter puluhan detik melewati 5 | Salah memakai `BCD_0to9` | Ganti dengan `BCD_0to5` |
