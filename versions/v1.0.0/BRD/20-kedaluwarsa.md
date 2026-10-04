> Versi: v1.0.0 | Status: disetujui | Menggantikan: -

# BRD 20 — Kedaluwarsa dan Notifikasi

## Konteks

Kedaluwarsa mencakup ambang waktu, notifikasi, dan aksi wajib agar bahan SEGERA dimasak bukan dibuang. Sumber isi lama: `notifikasi/10-sop-kedaluwarsa.md` seluruhnya.

## Kebutuhan Bisnis

- BR-201 Ambang dikunci dari selisih hari kalender: AMAN lebih dari 7 hari hijau, WASPADA 4 sampai 7 hari kuning H-7 sampai H-4, SEGERA 1 sampai 3 hari oranye H-3 sampai H-1, HARI_INI 0 hari merah pakai hari ini, KEDALUWARSA kurang dari 0 abu merah. Batch null tanggal tidak masuk SOP. Sisa masakan umur 2 hari langsung SEGERA sejak H+1.
- BR-202 Job server `expiry-sweep` jalan tiap hari 06:00 WIB, evaluasi per batch agregasi per item tampilkan terdekat saja. Push mobile hanya transisi naik tingkat, maksimal 1 push per item per hari, jam 06:30 sampai 20:00 WIB selebihnya antre.
- BR-203 Template baku dikunci: WASPADA sisa dan tanggal cocok dimasak minggu ini, SEGERA tinggal n hari lihat resep Pawonee atau catat pakai, HARI_INI pakai hari ini atau buang dan catat, KEDALUWARSA buang dan catat agar waste akurat.
- BR-204 Kanal Varian 1: mobile KMP push plus badge plus banner, web Bun badge plus banner tanpa push browser. Tanpa SMS, email, WhatsApp Varian 1.
- BR-205 Aksi wajib: WASPADA tombol lihat resep, SEGERA lihat resep plus catat pakai, HARI_INI catat pakai plus buang, KEDALUWARSA hanya buang dan catat dan terkunci dari FEFO.
- BR-206 Buang wajib alasan baku `busuk`, `berlendir dan berbau`, `lewat tanggal`, `tercemar`; menulis mutasi BUANG; sebagian diizinkan; yang dibuang masuk waste bulan berjalan.

## Metrik

- Persen batch SEGERA yang dipakai minimum 50 persen, push tepat waktu, buang tercatat 100 persen dengan alasan.

## Batasan

Batasan segmen ini: hanya ambang, jadwal, template, kanal, dan aksi. Di luar batas: skor resep Pawonee dan penawaran TitipO. Bahan beku memakai tanggal sama dengan label tambahan beku tanpa ubah ambang.
