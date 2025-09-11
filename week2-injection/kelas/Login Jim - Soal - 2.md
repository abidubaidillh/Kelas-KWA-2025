# Write-up: OWASP Juice Shop - Login Jim

<img width="437" height="199" alt="image" src="https://github.com/user-attachments/assets/20a4990a-0bb3-4394-90bc-b03e2b1f3d13" />

## 1. [Soal]
Masuk dengan akun pengguna Jim.

## 2. [Analisis Kerentanan]
Lab ini memiliki kerentanan SQL Injection yang sama seperti lab login administrator. Aplikasi rentan karena ia langsung menyisipkan input dari kolom email dan password ke dalam query SQL, tanpa validasi.

Query yang rentan kemungkinan besar terlihat seperti ini:
```
SELECT * FROM users WHERE email = '[input_email]' AND password = '[input_password]'
```
Tantangan di sini adalah mengetahui bahwa username yang digunakan adalah format email dan kemudian memanipulasi query tersebut agar dapat masuk sebagai pengguna "Jim" tanpa mengetahui _password_nya.

## 3. [Jawaban & Bukti]
### Metode Eksploitasi
Serangan ini menggunakan payload SQL Injection untuk membuat database mengabaikan pemeriksaan password bagi pengguna "Jim".

Payload yang digunakan: `jim@juice-sh-op'--`

Pencarian Informasi (Reconnaissance): Langkah pertama adalah menemukan format username yang benar. Dari ulasan produk, Anda berhasil menemukan bahwa username memiliki format email seperti `admin@juice-sh-op`.

<img width="706" height="697" alt="Screenshot 2025-09-11 102810" src="https://github.com/user-attachments/assets/c0586093-c3f7-45ff-8fdf-dca65a8fb9f2" />

### Injeksi Payload:

`jim@juice-sh-op'` (Tanda Petik Tunggal): Ini adalah username target, dan tanda petik di akhir menutup string email di query SQL.

`--`: Simbol ini berfungsi sebagai komentar, menginstruksikan database untuk mengabaikan sisa query, yaitu AND password = '[input_password]'.

### Langkah-langkah Eksekusi
#### 1. Buka halaman login.

Di kolom Email, masukkan payload: `jim@juice-sh-op'--`

Di kolom Password, masukkan teks apa saja, misalnya abcd.

<img width="654" height="723" alt="Screenshot 2025-09-11 103032" src="https://github.com/user-attachments/assets/57ca7441-bd31-48b2-9843-0e98cb0ba1ef" />

#### Klik tombol Log in.

Query yang disuntikkan akan membuat database mengizinkan Anda masuk sebagai Jim tanpa verifikasi password.

### Bukti

Tangkap layar dari dashboard yang menunjukkan Anda telah masuk sebagai Jim

<img width="1904" height="860" alt="Screenshot 2025-09-11 103045" src="https://github.com/user-attachments/assets/d75b6c4f-3fcd-4f8c-a7d8-27e20a30b9fc" />

## 4. [Catatan Hasil Percobaan]
Keberhasilan: Serangan berhasil.

Refleksi: Lab ini mengajarkan bahwa sebelum melakukan serangan, kita perlu melakukan pengumpulan informasi (seperti format username) untuk berhasil. Ini adalah langkah yang lebih realistis dibandingkan lab sebelumnya.

Pembelajaran: SQL Injection tidak terbatas pada nama pengguna; ia dapat terjadi pada parameter apa pun yang tidak divalidasi, termasuk alamat email.
