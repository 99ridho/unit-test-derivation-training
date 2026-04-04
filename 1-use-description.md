# Use Case Description: Penambahan Mata Kuliah ke KRS

## Normal flow

1. Mahasiswa memilih mata kuliah yang ditawarkan pada semester yang akan berjalan
2. Sistem menerima pemilihan mata kuliah yang dikirimkan oleh mahasiswa
3. Sistem melakukan validasi berikut secara berurutan: periode pengisian KRS, pemenuhan prasyarat, total SKS setelah dijumlah dengan SKS MK yang dipilih, dan mendeteksi konflik jadwal terhadap MK yang telah terdaftar pada KRS
4. Sistem mencatat mata kuliah terpilih pada KRS mahasiswa
5. Mahasiswa mendapatkan pesan bahwa MK telah ditambahkan pada KRS

## Alternate flow

1. Jika waktu saat ini diluar periode pengisian KRS, maka mahasiswa akan mendapatkan pesan "periode telah berakhir atau belum dimulai"
2. Jika mahasiswa belum mengambil MK prasyarat sebelumnya, maka mahasiswa akan mendapatkan pesan "MK prasyarat belum diambil, silakan mengambil prasyarat dahulu"
3. Jika SKS semester mahasiswa telah mencapai batas maksimal pada semester tersebut, maka mahasiswa akan mendapatkan pesan "SKS yang telah diambil telah mencapai batas maksimal"
4. Jika ternyata ditemukan konflik jadwal dengan MK pada KRS, maka mahasiswa akan mendapat pesan "ada jadwal yang konflik, silakan pilih jadwal yang lain"
