# Write-up: SQL injection attack, querying the database type and version on MySQL and Microsoft

<img width="1903" height="877" alt="Screenshot 2025-09-10 224859" src="https://github.com/user-attachments/assets/0a8b89e9-fe45-406f-9b2f-5a0976e9bd2d" />

## 1. [Soal]
Lab ini memiliki kerentanan SQL injection pada filter kategori produk. Anda dapat menggunakan serangan UNION untuk mengambil hasil dari query yang disuntikkan. Tujuan lab ini adalah menampilkan string versi database MySQL atau Microsoft.

<img width="1621" height="908" alt="Screenshot 2025-09-10 225245" src="https://github.com/user-attachments/assets/ab09bcfd-0300-488c-b970-186be47a5721" />


## 2. [Analisis Kerentanan]
Sama seperti lab Oracle, kerentanan terjadi karena aplikasi secara langsung memasukkan input pengguna ke dalam query SQL, yang kemungkinan besar terlihat seperti ini:
```
SELECT * FROM products WHERE category = '[input]' AND released = 1
```
Untuk lab ini, kita akan menggunakan operator UNION untuk menyuntikkan query baru yang akan mengambil informasi versi database. Namun, karena database target bukan Oracle, kita harus menggunakan sintaks yang berbeda.

## 3. [Jawaban & Bukti]
### Metode Eksploitasi
Serangan ini menggunakan UNION attack untuk menggabungkan hasil query asli dengan query yang kita buat untuk menampilkan versi database.

<img width="1601" height="910" alt="Screenshot 2025-09-10 225606" src="https://github.com/user-attachments/assets/2acb53b0-25e9-4f27-b464-9bb637633895" />


Payload yang digunakan: `' +UNION+SELECT+@@version,+NULL#`

`'`: Menutup string kategori asli.

`UNION`: Operator yang menggabungkan hasil dari dua query SELECT.

`SELECT @@version, NULL`: Memilih dua kolom. @@version adalah variabel sistem di MySQL dan Microsoft SQL Server yang berisi informasi versi. NULL adalah placeholder agar jumlah kolom cocok dengan query asli yang juga memiliki dua kolom.

`#`: Simbol ini adalah karakter komentar di MySQL dan Microsoft SQL Server. Ia berfungsi untuk menonaktifkan sisa dari query asli.

### Langkah-langkah Eksekusi dengan Burp Suite
#### 1. Siapkan Burp Suite: Pastikan Burp Suite berjalan dan browser Anda sudah dikonfigurasi untuk menggunakan proxy-nya. Aktifkan Intercept is on.

#### 2. Tangkap Permintaan: Di browser lab, klik salah satu kategori produk (misalnya Gifts). Permintaan GET akan tertangkap oleh Burp Suite.

<img width="1601" height="910" alt="Screenshot 2025-09-10 225606" src="https://github.com/user-attachments/assets/2acb53b0-25e9-4f27-b464-9bb637633895" />

#### 3. Modifikasi Permintaan: Di Burp Suite, pada panel permintaan, ubah nilai parameter category yang semula Gifts menjadi payload yang sudah disiapkan:
```
Gifts'+UNION+SELECT+@@version,+NULL#
```
<br> (Catatan: Karakter spasi akan diubah menjadi + atau %20 secara otomatis).

<img width="1601" height="910" alt="Screenshot 2025-09-10 225606" src="https://github.com/user-attachments/assets/50e54a48-64eb-4bed-9e4a-af01f2ceee1a" />


#### 4. Kirim Permintaan: Klik tombol Forward untuk mengirim permintaan yang sudah dimodifikasi ke server.

<img width="1595" height="904" alt="Screenshot 2025-09-10 225623" src="https://github.com/user-attachments/assets/b957b15d-7046-4396-8aa6-33e0a9bed98c" />


#### 5. Verifikasi: Database akan menjalankan query yang dimodifikasi. Sebagai hasilnya, halaman web akan menampilkan string versi database yang berhasil diambil. Lab akan secara otomatis ditandai sebagai "Solved".

<img width="1913" height="814" alt="Screenshot 2025-09-10 225642" src="https://github.com/user-attachments/assets/00f56531-ee41-4348-a31b-37fbafaa25e7" />

## 4. [Catatan Hasil Percobaan]
Keberhasilan: Serangan berhasil.

Refleksi: Lab ini mengajarkan kita tentang pentingnya memahami sintaks SQL yang berbeda untuk setiap jenis database. Apa yang berfungsi di Oracle (v$version) tidak akan berfungsi di MySQL atau Microsoft. Ini adalah langkah kunci dalam mengasah kemampuan penetration testing.
