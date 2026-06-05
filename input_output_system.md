# Input dan Output Sistem

Dokumen ini menjelaskan input, output, dan sinyal internal pada stopwatch digital `HH:MM:SS`.

## 1. Input Eksternal

| Input | Lebar | Aktif | Fungsi | Perilaku yang Diharapkan |
|---|---:|---|---|---|
| `CLK_1Hz` | 1 bit | Rising edge | Clock utama sistem | Counter bertambah setiap pulsa saat `EN=1`. |
| `START` | 1 bit | High / ditekan | Menjalankan stopwatch | `EN_FF.Q` menjadi 1. |
| `STOP` | 1 bit | High / ditekan | Menghentikan stopwatch | `EN_FF.Q` menjadi 0. |
| `RESET` | 1 bit | High / ditekan | Menghapus nilai waktu | Semua counter kembali ke 0 dan `EN=0`. |

## 2. Sinyal Internal

| Sinyal | Fungsi |
|---|---|
| `EN` | Enable global dari `EN_FF` ke counter pertama. |
| `CLR` | Jalur reset global ke semua counter. |
| `CARRY` | Sinyal terminal count dari satu digit ke digit berikutnya. |
| `Q[3:0]` | Nilai BCD dari setiap digit counter. |

## 3. Output Eksternal

| Output | Sumber | Fungsi |
|---|---|---|
| Display detik satuan | `SEC_ONES.Q[3:0]` | Menampilkan digit satuan detik. |
| Display detik puluhan | `SEC_TENS.Q[3:0]` | Menampilkan digit puluhan detik. |
| Display menit satuan | `MIN_ONES.Q[3:0]` | Menampilkan digit satuan menit. |
| Display menit puluhan | `MIN_TENS.Q[3:0]` | Menampilkan digit puluhan menit. |
| Display jam satuan | `HR_ONES.Q[3:0]` | Menampilkan digit satuan jam. |
| Display jam puluhan | `HR_TENS.Q[3:0]` | Menampilkan digit puluhan jam. |

## 4. Contoh Output Terukur

| Kondisi Uji | Input | Output yang Diharapkan |
|---|---|---|
| Kondisi awal | Power on atau reset | `00:00:00` |
| Start | Tekan `START` | `00:00:01`, `00:00:02`, dan seterusnya |
| Stop | Tekan `STOP` | Nilai display tidak berubah |
| Resume | Tekan `START` setelah stop | Hitungan lanjut dari nilai terakhir |
| Reset | Tekan `RESET` | `00:00:00` dan stopwatch berhenti |
| Carry detik-menit | Dari `00:00:59` tambah 1 detik | `00:01:00` |
| Carry menit-jam | Dari `00:59:59` tambah 1 detik | `01:00:00` |
