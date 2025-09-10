# Write-up: SQL injection attack, querying the database type and version on Oracle

<img width="1893" height="878" alt="Screenshot 2025-09-10 220715" src="https://github.com/user-attachments/assets/41241426-7eb0-4471-8a5a-ead2834c7d32" />


## 1. [Soal]
Lab ini memiliki kerentanan SQL injection pada filter kategori produk. Kita dapat menggunakan serangan UNION untuk mengambil hasil dari query yang disuntikkan. Tujuan lab ini adalah menampilkan string versi database Oracle.

## 2. [Analisis Kerentanan]
Sama seperti lab sebelumnya, kerentanan terjadi karena aplikasi secara langsung memasukkan input pengguna ke dalam query SQL, yang kemungkinan besar terlihat seperti ini:
```
SELECT * FROM products WHERE category = '[input]' AND released = 1
```
Untuk lab ini, kita tidak hanya akan membuat kondisi WHERE menjadi benar, tetapi juga akan menyuntikkan query lain menggunakan operator UNION untuk mengambil informasi sensitif dari tabel internal database.

## 3. [Jawaban & Bukti]
### Metode Eksploitasi
Serangan ini menggunakan UNION attack untuk menggabungkan hasil query asli dengan query yang kita buat untuk menampilkan versi database.

Payload yang digunakan: `' +UNION+SELECT+BANNER,+NULL+FROM+v$version--`

`'`: Menutup string kategori asli.

`UNION`: Operator yang menggabungkan hasil dari dua query SELECT.

`SELECT BANNER, NULL`: Memilih dua kolom. BANNER adalah kolom di tabel Oracle yang berisi informasi versi. NULL adalah placeholder agar jumlah kolom cocok dengan query asli yang juga memiliki dua kolom.

`FROM v$version`: Mengambil data dari tabel sistem v$version yang berisi informasi versi database Oracle.

`--`: Komentar yang menonaktifkan sisa dari query asli.

### Langkah-langkah Eksekusi dengan Burp Suite
1. Siapkan Burp Suite: Buka Burp Suite dan atur browser Kita untuk menggunakan proxy-nya. Pastikan fitur Intercept is on aktif di tab Proxy.

2. Tangkap Permintaan: Di browser lab, klik salah satu kategori produk (misalnya Toys Games). Permintaan GET yang dikirim ke server akan langsung tertangkap oleh Burp Suite.

<img width="1321" height="660" alt="Screenshot 2025-09-10 220214" src="https://github.com/user-attachments/assets/8598a8a6-24f7-4122-b37e-e0472da66fe0" />


4. Modifikasi Permintaan: Di Burp Suite, pada panel Raw atau Params, ubah nilai parameter category yang semula Gifts menjadi payload yang sudah disiapkan:
Toys+%26+Games'+UNION+SELECT+BANNER,+NULL+FROM+v$version--
<br> (Catatan: Karakter spasi akan diubah menjadi + atau %20 oleh browser).

5. Kirim Permintaan: Klik tombol Forward di Burp Suite untuk mengirim permintaan yang sudah dimodifikasi ke server.

<img width="1601" height="822" alt="Screenshot 2025-09-10 220244" src="https://github.com/user-attachments/assets/9a3f8b22-4cf6-47e3-bda9-c2b1f24bbc5e" />

6. Setelah berhasil jalankan show respon in browser copy url yang diberikan dan paste di tab broweser yang baru

<img width="810" height="365" alt="Screenshot 2025-09-10 220322" src="https://github.com/user-attachments/assets/2caa83a0-d98e-4d1d-8e32-f9f32a1d700b" />

<img width="1717" height="612" alt="Screenshot 2025-09-10 220352" src="https://github.com/user-attachments/assets/cf98e49c-a428-4041-bd6a-9efa2a6e3e01" />

7. Verifikasi: Database akan menjalankan query yang dimodifikasi. Sebagai hasilnya, halaman web akan menampilkan string versi database yang berhasil diambil.  Lab akan secara otomatis ditandai sebagai "Solved".

<img width="1858" height="868" alt="Screenshot 2025-09-10 220404" src="https://github.com/user-attachments/assets/a3bfcb69-b93d-405b-be0e-c92146a6ced7" />

## 4. [Catatan Hasil Percobaan]
Keberhasilan: Serangan berhasil.

Refleksi: Lab ini mengajarkan kita tentang serangan UNION attack yang lebih canggih dan bagaimana menggunakan Burp Suite untuk memanipulasi request yang dikirim ke server.

Pembelajaran: Memahami cara kerja database dan mengetahui tabel-tabel internalnya adalah kunci untuk mengeksploitasi kerentanan ini. Burp Suite sangat membantu untuk melihat dan memodifikasi permintaan secara detail.
