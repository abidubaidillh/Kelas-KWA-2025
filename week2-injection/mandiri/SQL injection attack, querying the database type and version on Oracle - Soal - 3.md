Write-up: SQL Injection - Mengambil Informasi Database Oracle
1. [Soal]
Lab ini memiliki kerentanan SQL injection pada filter kategori produk. Anda dapat menggunakan serangan UNION untuk mengambil hasil dari query yang disuntikkan. Tujuan lab ini adalah menampilkan string versi database Oracle.

2. [Analisis Kerentanan]
Sama seperti lab sebelumnya, kerentanan terjadi karena aplikasi secara langsung memasukkan input pengguna ke dalam query SQL, yang kemungkinan besar terlihat seperti ini:

SELECT * FROM products WHERE category = '[input]' AND released = 1

Untuk lab ini, kita tidak hanya akan membuat kondisi WHERE menjadi benar, tetapi juga akan menyuntikkan query lain menggunakan operator UNION untuk mengambil informasi sensitif dari tabel internal database.

3. [Jawaban & Bukti]
Metode Eksploitasi
Serangan ini menggunakan UNION attack untuk menggabungkan hasil query asli dengan query yang kita buat untuk menampilkan versi database.

Payload yang digunakan: ' +UNION+SELECT+BANNER,+NULL+FROM+v$version--

': Menutup string kategori asli.

UNION: Operator yang menggabungkan hasil dari dua query SELECT.

SELECT BANNER, NULL: Memilih dua kolom. BANNER adalah kolom di tabel Oracle yang berisi informasi versi. NULL adalah placeholder agar jumlah kolom cocok dengan query asli yang juga memiliki dua kolom.

FROM v$version: Mengambil data dari tabel sistem v$version yang berisi informasi versi database Oracle.

--: Komentar yang menonaktifkan sisa dari query asli.

Langkah-langkah Eksekusi dengan Burp Suite
Siapkan Burp Suite: Buka Burp Suite dan atur browser Anda untuk menggunakan proxy-nya. Pastikan fitur Intercept is on aktif di tab Proxy.

Tangkap Permintaan: Di browser lab, klik salah satu kategori produk (misalnya Gifts). Permintaan GET yang dikirim ke server akan langsung tertangkap oleh Burp Suite.

Modifikasi Permintaan: Di Burp Suite, pada panel Raw atau Params, ubah nilai parameter category yang semula Gifts menjadi payload yang sudah disiapkan:
Toys+%26+Games'+UNION+SELECT+BANNER,+NULL+FROM+v$version--
<br> (Catatan: Karakter spasi akan diubah menjadi + atau %20 oleh browser).

Kirim Permintaan: Klik tombol Forward di Burp Suite untuk mengirim permintaan yang sudah dimodifikasi ke server.

Verifikasi: Database akan menjalankan query yang dimodifikasi. Sebagai hasilnya, halaman web akan menampilkan string versi database yang berhasil diambil.  Lab akan secara otomatis ditandai sebagai "Solved".

4. [Catatan Hasil Percobaan]
Keberhasilan: Serangan berhasil.

Refleksi: Lab ini mengajarkan kita tentang serangan UNION attack yang lebih canggih dan bagaimana menggunakan Burp Suite untuk memanipulasi request yang dikirim ke server.

Pembelajaran: Memahami cara kerja database dan mengetahui tabel-tabel internalnya adalah kunci untuk mengeksploitasi kerentanan ini. Burp Suite sangat membantu untuk melihat dan memodifikasi permintaan secara detail.