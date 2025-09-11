Write-up: OWASP Juice Shop - Login Admin
1. [Soal]
Masuk ke akun pengguna administrator.

2. [Analisis Kerentanan]
Lab ini rentan terhadap SQL Injection karena aplikasi web secara langsung menyisipkan input dari kolom username dan password ke dalam query SQL tanpa validasi yang tepat.

Query yang rentan kemungkinan besar terlihat seperti ini:

SELECT * FROM users WHERE username = '[input_username]' AND password = '[input_password]'

Tujuan kita adalah mengubah query ini agar selalu bernilai benar, sehingga database mengizinkan kita masuk tanpa mengetahui password yang benar.

3. [Jawaban & Bukti]
Metode Eksploitasi
Serangan ini menggunakan payload Login Bypass yang membuat database mengabaikan pemeriksaan password.

Payload yang digunakan: ' or 1=1--

' (Tanda Petik Tunggal): Digunakan untuk menutup string username dari query SQL asli.

or 1=1: Ini adalah kondisi logis yang selalu benar. Ketika digabungkan dengan OR, seluruh kondisi WHERE menjadi benar.

--: Simbol ini adalah komentar di SQL. Ia memberitahu database untuk mengabaikan semua teks yang mengikutinya, termasuk AND password = '[input_password]'.

Langkah-langkah Eksekusi
Buka halaman login dari OWASP Juice Shop.

Di kolom Username, masukkan payload: ' or 1=1--

Di kolom Password, masukkan teks apa saja, misalnya abcd.

Klik tombol Log in.

Query yang disuntikkan akan membuat database mengembalikan data pengguna administrator, dan Anda akan berhasil masuk.

Bukti
Tangkap layar dari halaman login dengan payload yang dimasukkan.

Tangkap layar dari dashboard yang menunjukkan Anda telah masuk sebagai administrator.

Pesan notifikasi "challenge solved" dari Juice Shop.

4. [Catatan Hasil Percobaan]
Keberhasilan: Serangan berhasil.

Refleksi: Lab ini adalah demonstrasi klasik dari bahaya SQL Injection. Ini menunjukkan bagaimana input yang tidak divalidasi dapat digunakan untuk melewati kontrol akses yang fundamental seperti autentikasi. Pemahaman ini sangat penting sebagai dasar bagi setiap profesional keamanan web.