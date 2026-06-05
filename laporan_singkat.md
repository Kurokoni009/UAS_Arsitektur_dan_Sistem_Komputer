# Laporan Singkat Proyek: Stopwatch Digital HH:MM:SS Berbasis Logisim Evolution

**Mata Kuliah**: Arsitektur dan Sistem Komputer  
**Dosen**: Dr. Widi Aribowo, S.T., M.T. dan Harmon Prayogi, M.Sc.  
**Program Studi**: S1 Kecerdasan Artifisial, FMIPA Universitas Negeri Surabaya  
**Tahun Akademik**: 2025/2026  
**Topik Proyek**: Digital Logic Design  
**Jenis Sistem**: Embedded system dalam simulator Logisim Evolution  

## 1. Pendahuluan

Stopwatch digital merupakan contoh sistem digital sekuensial karena outputnya tidak hanya ditentukan oleh input saat ini, tetapi juga oleh keadaan sebelumnya yang disimpan di flip-flop. Pada proyek ini, stopwatch dirancang dengan format `HH:MM:SS` menggunakan enam counter BCD yang disusun berantai. Rancangan ini relevan untuk mata kuliah Arsitektur dan Sistem Komputer karena memperlihatkan hubungan antara clock, register keadaan, control unit, sinyal enable, reset, dan antarmuka output digital.

Masalah utama dalam perancangan stopwatch bukan hanya membuat counter bertambah, tetapi memastikan setiap digit berubah pada waktu yang tepat. Digit satuan detik harus menghitung 0 sampai 9, digit puluhan detik harus menghitung 0 sampai 5, lalu carry harus diteruskan ke menit dan jam. Karena itu, proyek ini menekankan rancangan carry chain, validitas format waktu, dan konsistensi reset global.

## 2. Tujuan

Tujuan proyek ini adalah merancang dan mensimulasikan stopwatch digital `HH:MM:SS` di Logisim Evolution. Secara teknis, proyek ini bertujuan mengimplementasikan counter BCD `0–9` dan `0–5`, menyusun carry chain dari detik ke menit dan jam, menerapkan kontrol `START`, `STOP`, dan `RESET`, serta menguji output sistem secara terukur melalui simulasi.

## 3. Desain Sistem

Sistem terdiri dari empat bagian utama. Bagian pertama adalah input, yaitu `CLK_1Hz`, `START`, `STOP`, dan `RESET`. Bagian kedua adalah control unit sederhana berupa `EN_FF` yang menyimpan status berjalan atau berhenti. Bagian ketiga adalah rantai counter BCD, yaitu `SEC_ONES`, `SEC_TENS`, `MIN_ONES`, `MIN_TENS`, `HR_ONES`, dan `HR_TENS`. Bagian keempat adalah output berupa enam display 7-segment mode BCD.

Rangkaian memakai dua jenis counter. Counter `BCD_0to9` digunakan untuk digit satuan detik, satuan menit, satuan jam, dan puluhan jam. Counter `BCD_0to5` digunakan untuk puluhan detik dan puluhan menit. Pemisahan ini diperlukan agar tampilan waktu tetap valid, terutama pada transisi `59` ke `00`.

## 4. Cara Kerja Sistem

Saat sistem pertama kali dijalankan, `EN_FF` bernilai 0 sehingga stopwatch berada pada kondisi berhenti. Ketika tombol `START` ditekan, `EN_FF` menghasilkan `EN=1`. Sinyal ini mengaktifkan `SEC_ONES`, sehingga counter satuan detik bertambah pada setiap pulsa `CLK_1Hz`.

Ketika `SEC_ONES` mencapai nilai terminal 9, counter menghasilkan `CARRY` untuk mengaktifkan `SEC_TENS`. Ketika `SEC_TENS` mencapai nilai 5, carry diteruskan ke `MIN_ONES`. Pola yang sama berlanjut sampai `HR_TENS`. Dengan mekanisme ini, sistem dapat melakukan transisi waktu seperti `00:00:59 → 00:01:00` dan `00:59:59 → 01:00:00`.

Tombol `STOP` membuat `EN=0`, sehingga counter menahan nilai terakhir. Tombol `RESET` mengaktifkan jalur `CLR` global. Sinyal ini menghapus seluruh counter dan mengembalikan display ke `00:00:00`. Reset juga perlu diarahkan ke `EN_FF` agar stopwatch berhenti setelah kembali ke nol.

## 5. Input dan Output Sistem

| Jenis | Nama Sinyal | Fungsi |
|---|---|---|
| Input | `CLK_1Hz` | Pulsa utama pencacahan waktu. |
| Input | `START` | Mengaktifkan mode running. |
| Input | `STOP` | Menghentikan pencacahan pada nilai terakhir. |
| Input | `RESET` | Mengembalikan semua digit ke nol. |
| Internal | `EN` | Enable global untuk menjalankan counter pertama. |
| Internal | `CLR` | Clear global untuk seluruh counter. |
| Internal | `CARRY` | Mengaktifkan digit berikutnya pada terminal count. |
| Output | `Q[3:0]` | Nilai BCD setiap digit. |
| Output | 7-segment display | Menampilkan `HH:MM:SS`. |

## 6. Pengujian

Pengujian dilakukan dengan pendekatan black-box dan white-box. Pengujian black-box memeriksa respons display terhadap tombol `START`, `STOP`, dan `RESET`. Pengujian white-box memeriksa sinyal internal seperti `EN`, `CLR`, `Q[3:0]`, dan `CARRY` pada setiap counter.

Skenario utama yang harus diuji meliputi kondisi awal `00:00:00`, start dari keadaan diam, stop saat berjalan, resume dari nilai terakhir, reset saat diam, reset saat berjalan, transisi `00:00:59 → 00:01:00`, transisi `00:59:59 → 01:00:00`, dan wrap-around dari `99:59:59` ke `00:00:00` jika counter jam tetap memakai format dua digit 0 sampai 9.

## 7. Analisis dan Evaluasi

Kelebihan utama sistem adalah struktur modular. Counter `BCD_0to9` dan `BCD_0to5` dapat diuji secara terpisah sebelum dihubungkan pada top-level. Penggunaan tunnel `EN` dan `CLR` juga membuat wiring lebih rapi dan memudahkan debugging. Selain itu, display BCD langsung menyederhanakan output karena nilai counter tidak perlu melewati decoder tambahan.

Keterbatasan sistem berada pada carry ripple dan format jam. Ripple carry dapat menimbulkan glitch singkat pada simulasi berkecepatan tinggi karena sinyal carry berpindah secara bertahap dari satu digit ke digit berikutnya. Format jam juga belum mengikuti standar 24 jam karena `HR_TENS` masih berupa counter `0–9`. Dengan demikian, sistem lebih tepat disebut stopwatch dua digit jam, bukan real-time clock harian.

## 8. Kesimpulan

Proyek ini berhasil merancang struktur stopwatch digital `HH:MM:SS` berbasis counter BCD di Logisim Evolution. Rancangan memenuhi karakter utama Digital Logic Design karena menggunakan clock, flip-flop, counter, sinyal enable, reset global, carry chain, dan output display digital. Agar memenuhi syarat UAS secara penuh, repository perlu memuat file `.circ` pada folder `src`, dokumentasi pada folder `docs`, hasil pengujian pada folder `test`, README yang jelas, serta link video demo YouTube berdurasi 10 sampai 15 menit.
