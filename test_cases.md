# Daftar Test Case Singkat

| ID | Tujuan | Prosedur Singkat | Kriteria Lulus |
|---|---|---|---|
| TC-01 | Memeriksa initial state | Jalankan simulasi tanpa input | Display `00:00:00`, `EN=0` |
| TC-02 | Memeriksa START | Tekan `START` | Counter bertambah setiap clock |
| TC-03 | Memeriksa STOP | Tekan `STOP` saat running | Display menahan nilai terakhir |
| TC-04 | Memeriksa RESUME | Tekan `START` setelah STOP | Counter lanjut dari nilai terakhir |
| TC-05 | Memeriksa RESET saat idle | Tekan `RESET` saat stopped | Display `00:00:00`, `EN=0` |
| TC-06 | Memeriksa RESET saat running | Tekan `RESET` saat running | Display `00:00:00`, `EN=0` |
| TC-07 | Memeriksa carry detik | Uji transisi `00:00:59` | Output `00:01:00` |
| TC-08 | Memeriksa carry menit | Uji transisi `00:59:59` | Output `01:00:00` |
| TC-09 | Memeriksa wrap maksimum | Uji `99:59:59 + 1` | Output `00:00:00`, jika desain jam 00–99 |
