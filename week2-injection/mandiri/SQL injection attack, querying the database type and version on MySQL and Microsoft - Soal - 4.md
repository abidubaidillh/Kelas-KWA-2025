Write-up: SQL Injection - Mengambil Informasi Database MySQL/Microsoft
1. [Soal]
Lab ini memiliki kerentanan SQL injection pada filter kategori produk. Anda dapat menggunakan serangan UNION untuk mengambil hasil dari query yang disuntikkan. Tujuan lab ini adalah menampilkan string versi database MySQL atau Microsoft.

2. [Analisis Kerentanan]
Sama seperti lab Oracle, kerentanan terjadi karena aplikasi secara langsung memasukkan input pengguna ke dalam query SQL, yang kemungkinan besar terlihat seperti ini:

SELECT * FROM products WHERE category = '[input]' AND released = 1

Untuk lab ini, kita akan menggunakan operator UNION untuk menyuntikkan query baru yang akan mengambil informasi versi database. Namun, karena database target bukan Oracle, kita harus menggunakan sintaks yang berbeda.

3. [Jawaban & Bukti]
Metode Eksploitasi
Serangan ini menggunakan UNION attack untuk menggabungkan hasil query asli dengan query yang kita buat untuk menampilkan versi database.

Payload yang digunakan: ' +UNION+SELECT+@@version,+NULL#

': Menutup string kategori asli.

UNION: Operator yang menggabungkan hasil dari dua query SELECT.

SELECT @@version, NULL: Memilih dua kolom. @@version adalah variabel sistem di MySQL dan Microsoft SQL Server yang berisi informasi versi. NULL adalah placeholder agar jumlah kolom cocok dengan query asli yang juga memiliki dua kolom.

#: Simbol ini adalah karakter komentar di MySQL dan Microsoft SQL Server. Ia berfungsi untuk menonaktifkan sisa dari query asli.

Langkah-langkah Eksekusi dengan Burp Suite
Siapkan Burp Suite: Pastikan Burp Suite berjalan dan browser Anda sudah dikonfigurasi untuk menggunakan proxy-nya. Aktifkan Intercept is on.

Tangkap Permintaan: Di browser lab, klik salah satu kategori produk (misalnya Gifts). Permintaan GET akan tertangkap oleh Burp Suite.

Modifikasi Permintaan: Di Burp Suite, pada panel permintaan, ubah nilai parameter category yang semula Gifts menjadi payload yang sudah disiapkan:
Gifts'+UNION+SELECT+@@version,+NULL#
<br> (Catatan: Karakter spasi akan diubah menjadi + atau %20 secara otomatis).

Kirim Permintaan: Klik tombol Forward untuk mengirim permintaan yang sudah dimodifikasi ke server.

Verifikasi: Database akan menjalankan query yang dimodifikasi. Sebagai hasilnya, halaman web akan menampilkan string versi database yang berhasil diambil. Lab akan secara otomatis ditandai sebagai "Solved".

4. [Catatan Hasil Percobaan]
Keberhasilan: Serangan berhasil.

Refleksi: Lab ini mengajarkan kita tentang pentingnya memahami sintaks SQL yang berbeda untuk setiap jenis database. Apa yang berfungsi di Oracle (v$version) tidak akan berfungsi di MySQL atau Microsoft. Ini adalah langkah kunci dalam mengasah kemampuan penetration testing.