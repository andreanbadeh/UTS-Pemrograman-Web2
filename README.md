# Eksperimen SQL Injection pada Sistem Login PHP: Dari Mode Rentan ke Mode Aman
NAMA : ANDREAN PUTRA ARYA 

NIM : 312410341

KELAS : I241E

# Pendahuluan :
Dalam pengembangan aplikasi web, banyak developer fokus pada fitur dan tampilan, tetapi sering mengabaikan aspek keamanan. Padahal, celah kecil dalam penanganan input bisa berdampak besar terhadap keamanan sistem.

Pada artikel ini, saya melakukan eksperimen sederhana menggunakan PHP dan MySQL di lingkungan Ubuntu (tanpa XAMPP) untuk memahami salah satu kerentanan paling umum, yaitu SQL Injection. Menariknya, saya membangun dua mode dalam satu sistem: mode rentan dan mode aman, sehingga perbedaannya bisa langsung dibandingkan.

# Apa itu SQL Injection?
SQL Injection adalah teknik serangan di mana penyerang memasukkan perintah SQL ke dalam input aplikasi (misalnya form login) untuk memanipulasi query yang dijalankan oleh database.

Jika tidak ditangani dengan benar, serangan ini bisa:

  - Melewati sistem login tanpa password
  - Mengakses data sensitif
  - Mengubah atau menghapus isi database

Fase 1: Membangun Sistem Login

![gambar](https://github.com/andreanbadeh/UTS-Pemrograman-Web2/blob/a9fbab98ae9d2173aa97b87fccc62c87770cee21/gambar/1.jpg)

Gambar 1. Tampilan awal halaman login

Saya membuat sistem login sederhana menggunakan PHP dan MySQL yang berjalan di Ubuntu. Dalam kode ini, saya menambahkan variabel $mode untuk menentukan apakah sistem berjalan dalam kondisi rentan atau aman.

($mode = “rentan”; // bisa diganti jadi “aman”)

Mode Rentan (SQL Injection Vulnerable)

Pada mode ini, input dari user langsung dimasukkan ke dalam query SQL tanpa filter:

($query = “SELECT id, role FROM users
WHERE username=’$username’
AND password=’$password’”;)

Masalah utama:

  - Tidak ada validasi
  - Tidak ada pemisahan antara data dan query

![gambar](https://github.com/andreanbadeh/UTS-Pemrograman-Web2/blob/b18780f5f583f63d74c9ad2b4c798fbc8bc5b9b0/gambar/2.jpg)

Gambar 2. Percobaan login dengan data salah

Fase 2: Eksploitasi SQL Injection

Sebagai penyerang, saya mencoba login tanpa mengetahui password yang benar.

Input yang digunakan:

Username & Passwd: admin’ — & bebas

(Query yang terbentuk:
SELECT id, role FROM users
WHERE username=’admin’ — ‘
AND password=’bebas’;)

Tanda — berfungsi sebagai komentar, sehingga bagian password diabaikan.

![gambar](https://github.com/andreanbadeh/UTS-Pemrograman-Web2/blob/c0a59cc93d446366e9b2310fc4d91a0b2f25309d/gambar/3.jpg)

Gambar 3. Login berhasil menggunakan SQL Injection (mode rentan)

Hasil Eksperimen
Write on Medium

Login berhasil meskipun password salah.

Bahkan sistem menampilkan:

( Login Berhasil (RENTAN) )

Ini membuktikan bahwa sistem benar-benar bisa ditembus hanya dengan manipulasi input.

gambar

Gambar 4. Halaman dashboard setelah berhasil login

Fase 3: Mode Aman (Mitigasi)

Untuk mengatasi masalah ini, saya menggunakan Prepared Statement.

($stmt = $conn->prepare(“SELECT id, role FROM users WHERE username=? AND password=?”);
$stmt->bind_param(“ss”, $username, $password);
$stmt->execute();)

Kenapa Ini Aman?

  - Query dan input dipisahkan
  - Input tidak bisa diinterpretasikan sebagai perintah SQL
  - Database hanya membaca input sebagai data

Hasil Pengujian Ulang

Ketika mencoba input SQL Injection yang sama:

admin’ —

Login gagal, karena sistem tidak lagi tertipu oleh manipulasi query.

Analisis Perbandingan :
Mode : Hasil
Rentan : Bisa login tanpa password
Aman : Tidak bisa ditembus

Kesimpulannya: Perbedaan kecil dalam cara menulis query bisa berdampak besar terhadap keamanan sistem.

# Kesimpulan

Dari eksperimen ini, saya menyadari bahwa keamanan bukanlah fitur tambahan, melainkan bagian inti dari pengembangan aplikasi.

SQL Injection terjadi bukan karena teknik hacker yang rumit, tetapi karena kesalahan dasar dalam menangani input pengguna.

Prinsip penting yang harus diingat: “Never Trust User Input.” Dengan menggunakan prepared statement dan validasi input, kita bisa mencegah celah ini secara efektif.

# Referensi

- OWASP — SQL Injection
https://owasp.org/www-community/attacks/SQL_Injection

- PHP Documentation — MySQLi Prepared Statements
https://www.php.net/manual/en/mysqli.prepare.php

- MDN Web Docs — Web Security
https://developer.mozilla.org/en-US/docs/Web/Security
