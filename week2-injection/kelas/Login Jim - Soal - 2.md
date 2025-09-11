Write-up: OWASP Juice Shop - Login Jim
1. [Soal]
Masuk dengan akun pengguna Jim.

2. [Analisis Kerentanan]
Lab ini memiliki kerentanan SQL Injection yang sama seperti lab login administrator. Aplikasi rentan karena ia langsung menyisipkan input dari kolom email dan password ke dalam query SQL, tanpa validasi.

Query yang rentan kemungkinan besar terlihat seperti ini:

SELECT * FROM users WHERE email = '[input_email]' AND password = '[input_password]'

Tantangan di sini adalah mengetahui bahwa username yang digunakan adalah format email dan kemudian memanipulasi query tersebut agar dapat masuk sebagai pengguna "Jim" tanpa mengetahui _password_nya.

3. [Jawaban & Bukti]
Metode Eksploitasi
Serangan ini menggunakan payload SQL Injection untuk membuat database mengabaikan pemeriksaan password bagi pengguna "Jim".

Payload yang digunakan: jim@juice-sh-op'--

Pencarian Informasi (Reconnaissance): Langkah pertama adalah menemukan format username yang benar. Dari ulasan produk, Anda berhasil menemukan bahwa username memiliki format email seperti admin@juice-sh-op.

Injeksi Payload:

jim@juice-sh-op' (Tanda Petik Tunggal): Ini adalah username target, dan tanda petik di akhir menutup string email di query SQL.

--: Simbol ini berfungsi sebagai komentar, menginstruksikan database untuk mengabaikan sisa query, yaitu AND password = '[input_password]'.

Langkah-langkah Eksekusi
Buka halaman login.

Di kolom Email, masukkan payload: jim@juice-sh-op'--

Di kolom Password, masukkan teks apa saja, misalnya abcd.

Klik tombol Log in.

Query yang disuntikkan akan membuat database mengizinkan Anda masuk sebagai Jim tanpa verifikasi password.

Bukti
Tangkap layar dari halaman login dengan payload yang dimasukkan.

Tangkap layar dari dashboard yang menunjukkan Anda telah masuk sebagai Jim, dan notifikasi "challenge solved".

4. [Catatan Hasil Percobaan]
Keberhasilan: Serangan berhasil.

Refleksi: Lab ini mengajarkan bahwa sebelum melakukan serangan, kita perlu melakukan pengumpulan informasi (seperti format username) untuk berhasil. Ini adalah langkah yang lebih realistis dibandingkan lab sebelumnya.

Pembelajaran: SQL Injection tidak terbatas pada nama pengguna; ia dapat terjadi pada parameter apa pun yang tidak divalidasi, termasuk alamat email.