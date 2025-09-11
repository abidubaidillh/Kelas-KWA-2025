# Write-up: Broken Access Control - Web3 Sandbox

![alt text](image.png)

## 1. [Soal]
Temukan code sandbox yang tidak sengaja terpublikasi untuk menulis smart contracts secara langsung.

## 2. [Analisis Kerentanan]
Lab ini adalah contoh klasik dari kerentanan Broken Access Control yang secara spesifik merupakan jenis serangan Force Browsing atau CWE-425.

Kerentanan terjadi karena pengembang mengandalkan security by obscurity, yaitu menyembunyikan halaman (web3-sandbox) daripada melindunginya dengan pemeriksaan akses yang tepat. Mereka berasumsi bahwa karena tidak ada tautan yang mengarah ke halaman tersebut, maka tidak ada pengguna yang dapat menemukannya.

## 3. [Jawaban & Bukti]
### Metode Eksploitasi
Serangan ini terdiri dari dua langkah utama: pengumpulan informasi (untuk menemukan path yang tersembunyi) dan eksploitasi (untuk mengakses path tersebut secara langsung).

#### Langkah 1: Pengumpulan Informasi (Reconnaissance)
Penyerang memeriksa kode sumber aplikasi, seperti file main.js, untuk menemukan petunjuk tentang halaman yang mungkin tidak terlihat di antarmuka pengguna. Di sana, mereka menemukan nama path mencurigakan seperti web3-sandbox.

#### Langkah 2: Eksploitasi (Force Browsing)
Dengan mengetahui nama path tersebut, penyerang langsung memasukkannya ke dalam URL browser.

Payload: `https://juice-shop.herokuapp.com/#/web3-sandbox`

Saat URL diakses, aplikasi mengizinkan pengguna untuk melihat halaman tersebut karena tidak ada pemeriksaan otorisasi yang dilakukan di sisi server. Halaman ini adalah sandbox yang tersembunyi, dan dengan mengaksesnya, challenge pun selesai.

### Bukti
Tangkapan layar dari file main.js yang menunjukkan path web3-sandbox.

Tangkapan layar dari halaman web3-sandbox yang berhasil diakses.

Notifikasi "challenge solved" dari Juice Shop.

## 4. [Catatan Hasil Percobaan]
Keberhasilan: Serangan berhasil.

Refleksi: Lab ini menekankan pentingnya pemeriksaan akses di sisi server. Jika sebuah halaman seharusnya tidak dapat diakses oleh semua orang, server harus memverifikasi peran atau izin pengguna untuk setiap permintaan, tanpa memedulikan apakah URL-nya mudah ditebak atau tidak.

Pembelajaran: Bergantung pada security by obscurity adalah praktik yang sangat berbahaya karena penyerang selalu dapat menemukan petunjuk tersembunyi.