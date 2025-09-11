# Write-up: Broken Access Control - Web3 Sandbox

<img width="438" height="234" alt="image" src="https://github.com/user-attachments/assets/63af872c-d742-4382-a032-18456ede6509" />

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

<img width="875" height="985" alt="Screenshot 2025-09-11 143148" src="https://github.com/user-attachments/assets/36195acf-94cc-45e4-a1db-89a1b501de3c" />


#### Langkah 2: Eksploitasi (Force Browsing)
Dengan mengetahui nama path tersebut, penyerang langsung memasukkannya ke dalam URL browser.

<img width="1911" height="891" alt="Screenshot 2025-09-11 135449" src="https://github.com/user-attachments/assets/1b4ce0d8-d1a3-4b0d-a103-60c2f3965e36" />

Payload: `https://juice-shop.herokuapp.com/#/web3-sandbox`

Saat URL diakses, aplikasi mengizinkan pengguna untuk melihat halaman tersebut karena tidak ada pemeriksaan otorisasi yang dilakukan di sisi server. Halaman ini adalah sandbox yang tersembunyi, dan dengan mengaksesnya, challenge pun selesai.

<img width="1900" height="882" alt="Screenshot 2025-09-11 135838" src="https://github.com/user-attachments/assets/79ecab1a-136f-4274-941a-17fb975dfc08" />

## 4. [Catatan Hasil Percobaan]
Keberhasilan: Serangan berhasil.

Refleksi: Lab ini menekankan pentingnya pemeriksaan akses di sisi server. Jika sebuah halaman seharusnya tidak dapat diakses oleh semua orang, server harus memverifikasi peran atau izin pengguna untuk setiap permintaan, tanpa memedulikan apakah URL-nya mudah ditebak atau tidak.

Pembelajaran: Bergantung pada security by obscurity adalah praktik yang sangat berbahaya karena penyerang selalu dapat menemukan petunjuk tersembunyi.
