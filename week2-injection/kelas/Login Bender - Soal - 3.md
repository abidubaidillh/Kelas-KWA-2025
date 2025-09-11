Write-up: OWASP Juice Shop - Login Bender
1. [Soal]
Masuk ke akun pengguna Bender.

2. [Analisis Kerentanan]
Lab ini kembali menunjukkan kerentanan SQL Injection yang sama pada fungsi login. Aplikasi rentan karena ia langsung menyisipkan input dari kolom email dan password ke dalam query SQL tanpa sanitasi yang tepat.

Query yang rentan memiliki format:
SELECT * FROM users WHERE email = '[input_email]' AND password = '[input_password]'

Tujuan kita adalah mengeksploitasi input email untuk mem-bypass password dan masuk sebagai pengguna Bender.

3. [Jawaban & Bukti]
Metode Eksploitasi
Sama seperti lab sebelumnya, kita akan menggunakan payload SQL Injection untuk membuat database mengabaikan pemeriksaan password bagi pengguna target.

Payload yang digunakan: bender@juice-sh-op'--

bender@juice-sh-op' (Tanda Petik Tunggal): Ini adalah username target, dan tanda petik di akhir menutup string email di query SQL.

--: Simbol komentar ini menginstruksikan database untuk mengabaikan sisa query yang asli, termasuk bagian AND password = '[input_password]'.

Langkah-langkah Eksekusi
Buka halaman login.

Di kolom Email, masukkan payload: bender@juice-sh-op'--

Di kolom Password, masukkan teks apa saja.

Klik tombol Log in.

Query yang disuntikkan akan membuat database mengizinkan Anda masuk sebagai Bender tanpa verifikasi password.

Bukti
Tangkap layar dari halaman login dengan payload yang dimasukkan.

Tangkap layar dari dashboard yang menunjukkan Anda telah masuk sebagai Bender, serta notifikasi "challenge solved".

4. [Catatan Hasil Percobaan]
Keberhasilan: Serangan berhasil.

Refleksi: Lab ini berfungsi sebagai penguatan konsep. Ia menunjukkan bahwa kerentanan SQL Injection yang ditemukan di satu tempat sering kali dapat dieksploitasi dengan pola yang sama di tempat lain pada aplikasi yang sama.

Pembelajaran: Mengidentifikasi pola adalah keterampilan penting. Dalam kasus ini, pola username@domain.com'-- berfungsi untuk beberapa akun karena kesalahan yang mendasar dan sama di seluruh sistem login aplikasi.