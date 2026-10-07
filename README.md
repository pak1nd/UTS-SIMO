# UTS — Sistem Informasi Manajemen Olahraga

Ujian Tengah Semester online pilihan ganda untuk mata kuliah Sistem Informasi Manajemen
Olahraga (SIMO), Program Studi Pendidikan Kepelatihan Olahraga, FIK Universitas Negeri Medan.

Cakupan materi: **Pertemuan 1–6**.

| Pertemuan | Materi | Soal |
|---|---|---|
| 1 | Konsep Dasar SIMO | 7 |
| 2 | Komponen SIMO | 7 |
| 3 | Basis Data Olahraga Sederhana | 7 |
| 4 | Analisis Kebutuhan Informasi | 8 |
| 5 | Pemodelan SIMO (DFD, UML, ERD) | 11 |
| 6 | Cloud Computing | 10 |

## Ketentuan pengerjaan

| Butir | Ketentuan |
|---|---|
| Jumlah soal | 50 pilihan ganda (A–E) |
| Batas waktu | 144 detik per soal (total paling lama 120 menit); soal yang lewat waktu tidak dapat dikerjakan lagi |
| Identitas | Nama, NIM, dan Kelas asal (diketik sendiri; kelas gabung) |
| Pengumpulan | Satu NIM hanya bisa mengumpulkan satu kali |
| Penilaian | Otomatis di server, skor 0–100 |
| Pengawasan | Perpindahan tab atau aplikasi dicatat; pada kali ketiga jawaban dikirim otomatis |

## Batas pengawasan yang perlu diketahui

Halaman web tidak dapat memblokir mahasiswa membuka Chrome, aplikasi lain, atau
perangkat kedua. Yang dapat dilakukan hanyalah mendeteksi saat halaman kehilangan
fokus, lalu mencatatnya. Untuk penguncian sesungguhnya diperlukan Safe Exam Browser
di lab komputer atau perangkat Android dalam kiosk mode terkelola.

Sinyal yang sama juga terpicu oleh telepon masuk, notifikasi, atau pergantian papan
ketik pada ponsel. Karena itu sistem memberi dua peringatan sebelum mengirim paksa,
dan seluruh kejadian tersimpan lengkap dengan waktunya pada tab `Hasil` sehingga
dosen dapat menilai sendiri mana yang benar-benar pelanggaran.

Urutan soal yang diacak dan pilihan jawaban yang diacak untuk setiap mahasiswa menjadi
penghalang utama terhadap kerja sama. Batas waktu per soal membuat soal yang terlewat
tidak bisa dikerjakan ulang.

## Susunan

```
index.html   frontend, dihosting di GitHub Pages (ada di repositori ini)
Code.gs      backend Apps Script + database Google Sheets (TIDAK ada di repositori ini)
```

`Code.gs` memuat bank soal beserta kunci jawabannya, sehingga disimpan terpisah oleh
dosen dan tidak diunggah ke repositori publik. Berkas ini ditempel langsung ke Apps Script
pada Spreadsheet dosen.

Frontend memanggil backend lewat JSONP, sehingga tidak terhalang CORS.
Kunci jawaban tidak pernah dikirim ke browser — penilaian sepenuhnya dilakukan di server.

## Pemasangan

### 1. Backend

1. Buat Google Spreadsheet baru memakai akun Gmail pribadi.
2. Buka **Extensions › Apps Script**, hapus isi bawaan, tempel isi `Code.gs`.
3. Simpan, pilih fungsi `setupDatabase`, jalankan, lalu izinkan akses.
   Tab `Soal`, `Hasil`, `Detail`, dan `Log` akan terbentuk dan terisi 50 soal.
4. **Deploy › New deployment › Web app**
   - Execute as: **Me**
   - Who has access: **Anyone**
5. Salin URL yang berakhiran `/exec`.

### 2. Frontend

1. Buka `index.html`, ganti nilai `API_URL` dengan URL `/exec` tadi.
2. Unggah `index.html` ke repositori GitHub.
3. **Settings › Pages › Deploy from a branch › main / root**.
4. Bagikan alamat halaman kepada mahasiswa.

Bila `API_URL` belum diganti, halaman menampilkan pesan "API_URL belum diisi" saat
mahasiswa menekan tombol mulai.

## Mengubah isi ujian

Semua soal berada pada larik `BANK_SOAL` di `Code.gs`, dengan format
`[pertemuan, pertanyaan, [lima pilihan], indeks kunci]` (indeks dihitung dari 0).
Setelah mengubah soal, jalankan `setupDatabase` sekali lagi atau pilih menu
**UTS SIMO › Perbarui bank soal** — tab `Soal` ditulis ulang, sedangkan `Hasil` dan
`Detail` tetap utuh. Setiap butir harus memiliki tepat lima pilihan yang berbeda;
`setupDatabase` menolak butir yang tidak memenuhi syarat dan menyebut nomornya.

Pengaturan lain ada pada objek `CFG`:

| Kunci | Fungsi |
|---|---|
| `DETIK_PER_SOAL` | Batas waktu tiap soal |
| `JUMLAH_SOAL` | Banyak soal yang diterima tiap mahasiswa |
| `SEIMBANG_PERTEMUAN` | Undian merata antar pertemuan (berlaku bila `JUMLAH_SOAL` lebih kecil dari isi bank) |
| `ACAK_SOAL` | Mengacak urutan soal per mahasiswa |
| `ACAK_PILIHAN` | Mengacak urutan pilihan A–E per mahasiswa |
| `BOLEH_ULANG` | Mengizinkan satu NIM mengumpulkan lebih dari sekali |

Bila `DETIK_PER_SOAL` atau `JUMLAH_SOAL` diubah, samakan juga angka pada tampilan aturan
di `index.html` (blok `.aturan`).

Urutan pilihan A–E diacak ulang setiap kali `setupDatabase` dijalankan dengan susunan
yang merata: pada 50 soal, tiap huruf menjadi kunci tepat 10 kali. Selain itu, saat
ujian, urutan pilihan diacak lagi untuk setiap mahasiswa; huruf yang tampil di layar
mengikuti posisi, sedangkan penilaian memakai identitas pilihan sehingga tetap benar.

## Kelas gabung

Mata kuliah ini kelas pilihan yang digabung, sehingga seluruh mahasiswa mengerjakan satu
ujian yang sama dan tidak ada pemisahan soal per kelas. Pada layar awal, mahasiswa
mengetik sendiri kelas asalnya (misalnya `IKOR 2024 B`). Isian itu dirapikan (spasi, huruf
besar, paling banyak 30 karakter) lalu dicatat pada kolom **Kelas** di tab `Hasil` dan
`Detail`, sehingga rekap dapat disaring atau dikelompokkan per kelas asal.

Bank saat ini berisi 50 soal dan `JUMLAH_SOAL` bernilai 50, sehingga setiap mahasiswa
mengerjakan seluruh soal dengan urutan berbeda. Untuk mengundi sebagian saja, perbesar
bank soal lalu turunkan `JUMLAH_SOAL`. Bila isi bank kurang dari `JUMLAH_SOAL`, server
menolak dengan pesan yang menyebutkan kekurangannya.

## Pengawasan dan izin mengulang

Perpindahan aplikasi dideteksi hanya melalui `visibilitychange`, dan baru dihitung
bila halaman ditinggalkan minimal tiga detik. Pendeteksi `blur` sengaja tidak dipakai
karena pada Android ikut terpicu oleh notifikasi, peringatan baterai lemah, papan
ketik, dan bilah alamat browser — bukan hanya oleh perpindahan aplikasi yang
sesungguhnya.

Menu **UTS SIMO** pada Spreadsheet menyediakan perintah untuk mengelola izin mengulang:

| Perintah | Fungsi |
|---|---|
| Buka kunci satu NIM | Mengizinkan satu mahasiswa mengerjakan ulang |
| Buka kunci semua yang dikirim paksa | Membuka seluruh baris berstatus Dikirim paksa sekaligus |
| Lihat daftar yang terkunci | Menampilkan jumlah NIM terkunci dan siapa saja yang dikirim paksa |

Membuka kunci tidak menghapus apa pun. Baris lama tetap berada di tab `Hasil` dan
`Detail`, hanya statusnya berubah menjadi `Dibatalkan - izin mengulang` beserta
tanggalnya dan status semula, lalu barisnya diwarnai abu-abu. Pengecekan kunci
mengabaikan baris berstatus tersebut, sehingga NIM itu bisa mengerjakan lagi
sementara riwayat pemeriksaannya tetap utuh.

## Rekap nilai

Tab **Hasil** memuat waktu submit, nama, NIM, kelas, jumlah benar, salah, kosong,
skor, durasi pengerjaan (detik), jumlah pelanggaran, status, dan catatan waktu tiap
pelanggaran. Baris dengan pelanggaran diberi warna: kuning bila ada pelanggaran, merah
muda bila jawaban dikirim paksa. Tab **Detail** memuat jawaban tiap mahasiswa per soal
beserta hasilnya (Benar, Salah, atau Kosong), sehingga dapat direkap per soal untuk
melihat butir mana yang paling banyak salah.

Menu **UTS SIMO** juga menyediakan dua perintah lain: memperbarui bank soal, dan
mengosongkan rekap sebelum kelas berikutnya mengerjakan.
