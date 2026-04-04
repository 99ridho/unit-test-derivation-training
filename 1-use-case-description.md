# Use Case Description

## Normal flow

1. Mahasiswa memilih satu mata kuliah yang akan ditempuh
2. Sistem memproses pemilihan mata kuliah
3. Sistem melakukan pengecekan periode pengisian KRS, prasyarat mata kuliah, batas maksimal KRS serta konflik jadwal secara sekuensial
4. Sistem menambahkan mata kuliah terpilih kedalam KRS
5. Mahasiswa mendapatkan pesan konfirmasi bahwa mata kuliah telah ditambahkan pada KRS

## Alternate flow

1. Jika dilakukan diluar periode pengisian, maka sistem memberikan pesan “masa pengisian telah berakhir atau belum dimulai”
2. Jika mata kuliah prasyarat belum diambil, maka sistem memberikan pesan “harus menempuh mata kuliah prasyarat sebelum mengambil mata kuliah ini”
3. Jika total SKS setelah penambahan mata kuliah melebihi batas, maka sistem memberikan pesan “SKS sudah mencapai batas maksimal”
4. Jika adanya konflik jadwal dengan mata kuliah yang telah diambil pada KRS, maka sistem memberikan pesan “ada jadwal yang konflik, silakan pilih mata kuliah pada jadwal yang lain”
