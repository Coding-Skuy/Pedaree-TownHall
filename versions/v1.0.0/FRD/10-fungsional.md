> Versi: v1.0.0 | Status: disetujui | Menggantikan: -

# FRD 10 — Kebutuhan Fungsional

Dokumen ini menyatakan apa yang wajib dilakukan sistem, tanpa menyatakan cara implementasi.

## Inventori

- FR-001 Sistem wajib mengelola PantryItem dengan nama, kategori, satuan simpan dan tampil dari 13 baku, minimum, umur default, lokasi default, dan arsip.
- FR-002 Sistem wajib mengelola StokBatch dengan jumlah awal dan aktif, tanggal masuk dan kedaluwarsa, lokasi, sumber MASUK_BELANJA dan MASUK_PANEN dan MASUK_TITIPO dan MASUK_KOREKSI, dan status AKTIF dan HABIS dan KEDALUWARSA dan DIBUANG.
- FR-003 Sistem wajib menulis 1 MutasiStok tiap perubahan jumlahAktif dengan jenis PAKAI dan MASUK dan KOREKSI dan PINDAH dan BUANG plus alasan wajib untuk KOREKSI dan BUANG.
- FR-004 Sistem wajib menghitung stok tampil sebagai jumlah jumlahAktif batch AKTIF dan menentukan MENIPIS saat tampil kurang dari sama dengan minimum dan lebih dari 0 serta HABIS saat 0.
- FR-005 Sistem wajib menegakkan FEFO saat pakai dan koreksi negatif serta menolak batch KEDALUWARSA dipakai.

## Notifikasi

- FR-101 Sistem wajib mengevaluasi expiry per batch tiap 06:00 WIB dan menentukan AMAN dan WASPADA dan SEGERA dan HARI_INI dan KEDALUWARSA.
- FR-102 Sistem wajib mengirim push mobile transisi naik saja maksimal 1 per item per hari 06:30 sampai 20:00 WIB plus badge dan banner; web badge plus banner tanpa push.
- FR-103 Sistem wajib menyediakan aksi lihat resep dan catat pakai dan buang sesuai status dan mengunci KEDALUWARSA dari FEFO.

## Demand

- FR-201 Sistem wajib menerbitkan event `pedaree.stok.v1` jenis `stok.menipis` dan `stok.habis` dan `kedaluwarsa.dekat` pada transisi pemicu dengan debounce 24 jam.
- FR-202 Sistem wajib menyediakan daftar sinyal dan penerusan keranjang ke TitipO dengan status TERKIRIM dan ANTRE.
- FR-203 Sistem wajib menghitung stockoutRate dan wasteGram dan wasteRupiah dan waktu menipis ke habis serta event `stok_menipis` dan `stok_habis` dan `buang_catat` dan `sinyal_titipo` dan `pakai_dari_resep`.

## Lintas Segmen

- FR-301 Sistem wajib memvalidasi 13 satuan `kg`, `g`, `ons`, `buah`, `butir`, `siung`, `ikat`, `batang`, `lembar`, `bonggol`, `pak`, `ml`, `liter` dan menolak di luar daftar.
- FR-302 Sistem wajib mencatat setiap aksi dengan pembuat, waktu, dan versi untuk resolusi konflik opname 409.
- FR-303 Sistem wajib menegakkan JWT audien `pedaree` dan filter `X-Household-Id`: penghuni hanya datanya sendiri, TitipO hanya sinyal via pipeline, Pawonee hanya membaca stok untuk skor.

## Batasan

Batasan dokumen ini: hanya kebutuhan fungsional. Bahasa pemrograman, pustaka, basis data lokal, dan pola navigasi tidak diatur di sini dan hanya boleh muncul di FSD. Setiap kebutuhan di atas wajib punya uji penerimaan di PRD/30-kriteria.md.
