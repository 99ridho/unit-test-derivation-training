# Sistem Akademik Kampus

## Fitur: Penambahan Mata Kuliah ke KRS

Setiap awal semester, mahasiswa diwajibkan menyusun Kartu Rencana Studi (KRS) dengan memilih mata kuliah yang akan ditempuh. Salah satu operasi inti dalam proses ini adalah penambahan mata kuliah ke KRS oleh mahasiswa.

Ketika mahasiswa mengajukan penambahan suatu mata kuliah, sistem melakukan serangkaian pemeriksaan secara berurutan.

Pertama, sistem memverifikasi apakah periode pengisian KRS sedang berlangsung. Penambahan mata kuliah hanya diizinkan dalam rentang waktu yang telah ditetapkan oleh bagian akademik. Jika periode telah berakhir, pengajuan ditolak dan proses selesai.

Kedua, sistem memeriksa pemenuhan prasyarat mata kuliah yang diajukan. Sejumlah mata kuliah hanya dapat diambil apabila mahasiswa telah lulus mata kuliah tertentu pada semester sebelumnya. Jika prasyarat belum terpenuhi, pengajuan ditolak dan proses selesai.

Ketiga, sistem menghitung total SKS yang akan ditanggung mahasiswa apabila mata kuliah tersebut ditambahkan. Batas maksimal SKS per semester ditentukan berdasarkan IPS mahasiswa pada semester sebelumnya. Jika penambahan ini menyebabkan total SKS melebihi batas, pengajuan ditolak dan proses selesai.

Keempat, sistem mendeteksi kemungkinan konflik jadwal antara mata kuliah yang diajukan dengan mata kuliah yang sudah tercatat di KRS. Jika ditemukan tumpang tindih waktu, pengajuan ditolak dan proses selesai.

Apabila seluruh pemeriksaan berhasil dilewati, sistem mencatat mata kuliah ke dalam KRS mahasiswa dan memperbarui total SKS yang diambil pada semester berjalan.
