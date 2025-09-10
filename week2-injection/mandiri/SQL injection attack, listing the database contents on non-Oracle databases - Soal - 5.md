# Write-up: SQL injection attack, listing the database contents on non-Oracle databases

<img width="1901" height="979" alt="Screenshot 2025-09-10 231413" src="https://github.com/user-attachments/assets/f7ce0b41-55d4-4627-b315-d9eff69c2ce4" />

## 1. [Soal]
Lab ini memiliki kerentanan SQL injection di filter kategori produk. Kita perlu melakukan serangan UNION untuk menemukan nama tabel dan kolom yang berisi kredensial pengguna, kemudian mengambil kredensial tersebut untuk login sebagai administrator.

<img width="1899" height="969" alt="Screenshot 2025-09-10 231711" src="https://github.com/user-attachments/assets/83c8ac94-246c-433e-88f3-01f8654370b2" />

## 2. [Analisis Kerentanan]
Sama seperti lab sebelumnya, kerentanan terjadi karena aplikasi secara langsung memasukkan input pengguna ke dalam query SQL, yang memungkinkan kita menyuntikkan query tambahan dengan operator `UNION`.

Di lab ini, kita tidak mengetahui nama tabel atau kolom tempat kredensial pengguna disimpan. Oleh karena itu, kita harus melakukan "rantai serangan" dengan menginterogasi database itu sendiri menggunakan skema `information_schema`.

## 3. [Jawaban & Bukti]
### Langkah 1: Menemukan Nama Tabel Pengguna
Pertama, kita menyuntikkan query untuk mengambil nama-nama semua tabel dari `information_schema.tables` yang merupakan bagian dari setiap database non-Oracle.

Payload: `'+UNION+SELECT+table_name,+NULL+FROM+information_schema.tables--`

<img width="1898" height="978" alt="Screenshot 2025-09-10 233746" src="https://github.com/user-attachments/assets/2eeb4045-afe6-4779-b0bb-fc11a3aeafc4" />

Hasil: Kita menemukan tabel yang relevan: `users_pzneaq`.

### Langkah 2: Menemukan Nama Kolom Kredensial
Dengan nama tabel, kita menyuntikkan query lain untuk mengambil nama-nama kolom dari tabel tersebut melalui `information_schema.columns`.

<img width="1897" height="978" alt="Screenshot 2025-09-10 233905" src="https://github.com/user-attachments/assets/6148f1df-46f9-4ef9-ba55-6a32f8ad4309" />

Payload: `'+UNION+SELECT+column_name,+NULL+FROM+information_schema.columns+WHERE+table_name='users_pzneaq'--`

<img width="406" height="216" alt="Screenshot 2025-09-10 233942" src="https://github.com/user-attachments/assets/6d460ce9-6c4a-41ee-a603-77da5fac78a5" />
<img width="443" height="304" alt="Screenshot 2025-09-10 233952" src="https://github.com/user-attachments/assets/9c1bb1d3-922c-42bf-a84b-3a3c603fb2df" />

Hasil: Kita menemukan nama kolom `username_dhvbbq` dan `password_vyfgyf`.

### Langkah 3: Mengambil Data Kredensial
Sekarang kita memiliki semua informasi yang dibutuhkan. Kita dapat membuat query final untuk mengambil data dari kolom `username_dhvbbq` dan `password_vyfgyf` di dalam tabel `users_pzneaq`.

Payload: `'+UNION+SELECT+username_dhvbbq,password_vyfgyf+FROM+users_pzneaq--`

<img width="1906" height="982" alt="Screenshot 2025-09-10 234031" src="https://github.com/user-attachments/assets/022d127b-2254-498e-b1a0-201468d7fcac" />

Hasil: Halaman web menampilkan daftar lengkap username dan password.  Dari sana, Kita menemukan kredensial administrator:

<img width="484" height="233" alt="Screenshot 2025-09-10 234118" src="https://github.com/user-attachments/assets/00ac7d2c-2a11-4cb9-8a38-881c505c84de" />

Username: `administrator`

Password: `d7rxkwdmbjrrezdb61ia`

### Langkah 4: Login sebagai Administrator
Langkah terakhir adalah menggunakan kredensial yang ditemukan untuk login ke aplikasi. Setelah berhasil, lab akan selesai.

<img width="1898" height="936" alt="Screenshot 2025-09-10 234159" src="https://github.com/user-attachments/assets/1af94252-257e-4cf4-9323-327a8db2b3ee" />

### Bukti: Tangkapan layar yang menunjukkan Kita berhasil login sebagai administrator.

<img width="1902" height="894" alt="Screenshot 2025-09-10 234215" src="https://github.com/user-attachments/assets/2b76b5a2-1529-4468-ac22-34418ab17a42" />

## 4. [Catatan Hasil Percobaan]
Keberhasilan: Serangan berhasil.

Refleksi: Lab ini adalah puncak dari pemahaman UNION attack. Ini menunjukkan bagaimana serangkaian query dapat dihubungkan bersama untuk mengeksplorasi struktur database yang tidak diketahui.

Pembelajaran: Keterampilan ini, yang dikenal sebagai database enumeration, sangat penting dalam penetration testing karena memungkinkan Anda untuk beradaptasi dengan sistem yang berbeda dan mengambil informasi berharga secara sistematis.
