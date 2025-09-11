# Write-up: Broken Access Control - Mengakses Keranjang Belanja Lain

<img width="446" height="233" alt="Screenshot 2025-09-11 144741" src="https://github.com/user-attachments/assets/1c52e26c-66ef-4ba3-952f-a4144b1d0eba" />

## 1. [Soal]
Lihat keranjang belanja pengguna lain.

## 2. [Analisis Kerentanan]
Lab ini rentan terhadap kerentanan Insecure Direct Object Reference (IDOR). Kerentanan ini terjadi karena aplikasi menggunakan ID yang dapat ditebak (bid) untuk mengidentifikasi objek (keranjang belanja) dan gagal memvalidasi apakah pengguna yang sedang login memiliki hak untuk mengakses objek tersebut.

Selain itu, lab ini juga rentan terhadap Client-Side Parameter Tampering, di mana aplikasi mengandalkan data yang disimpan di sisi klien (Session Storage) dan menganggapnya aman, padahal data tersebut dapat dengan mudah dimodifikasi oleh penyerang.

## 3. [Jawaban & Bukti]
### Metode Eksploitasi
Serangan ini mengeksploitasi IDOR dengan memanipulasi nilai bid yang disimpan di Session Storage browser.

#### Langkah 1: Observasi Session Storage
Buka Developer Tools di browser Anda (biasanya dengan menekan F12 atau Ctrl+Shift+I). Buka tab Application lalu pilih Session Storage. Anda akan melihat pasangan kunci-nilai, termasuk bid yang berisi ID keranjang belanja Anda.

<img width="1910" height="982" alt="image" src="https://github.com/user-attachments/assets/11da648e-56d7-4f45-95b4-52b1cd7fad7b" />

#### Langkah 2: Modifikasi Nilai ID
Ubah nilai bid yang Anda lihat (misalnya dari 6 menjadi 1). Anda melakukan ini dengan mengklik dua kali pada nilai dan mengubahnya secara manual. Ini akan membuat browser Anda "berpikir" bahwa Anda memiliki ID keranjang belanja 1.

<img width="1919" height="990" alt="Screenshot 2025-09-11 152431" src="https://github.com/user-attachments/assets/bad18c37-1563-4fed-9a22-99046ac9ff07" />

#### Langkah 3: Muat Ulang Halaman
Muat ulang halaman atau navigasikan ke halaman keranjang belanja. Aplikasi akan menggunakan ID yang telah Anda modifikasi (1) untuk mengambil data. Karena tidak ada pemeriksaan di sisi server, aplikasi akan mengembalikan isi keranjang belanja dari pengguna dengan ID 1, dan challenge akan selesai.

<img width="1039" height="990" alt="Screenshot 2025-09-11 152431" src="https://github.com/user-attachments/assets/18d89ffe-feb0-460a-b120-9acc32a41f37" />

## 4. [Catatan Hasil Percobaan]
Keberhasilan: Serangan berhasil.

Refleksi: Lab ini menunjukkan bahwa kerentanan dapat bersembunyi di mana saja, tidak hanya di URL atau formulir.

Pembelajaran: Praktik keamanan yang buruk adalah mempercayai data yang datang dari sisi klien. Pengembang harus selalu memvalidasi dan memverifikasi hak akses pengguna untuk setiap permintaan data, tanpa memedulikan di mana ID objek disimpan.
