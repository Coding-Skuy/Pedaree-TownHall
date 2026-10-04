> Versi: v1.0.0 | Status: disetujui | Menggantikan: -

# BRD 10 — Inventori dan Satuan

## Konteks

Inventori mencakup kategori bahan, lokasi simpan, satuan baku, dan isi awal dapur. Sumber isi lama: `inventori/10-model-stok-dapur.md` bab kategori dan lokasi, `inventori/20-satuan-hortikultura.md` seluruhnya.

## Kebutuhan Bisnis

- BR-101 Kategori tetap Varian 1: SAYUR, BUMBU, BUAH, PROTEIN, KERING, CAIR, BEKU, SISA. Penambahan kategori baru butuh perubahan skema minor.
- BR-102 Lokasi simpan tetap: KULKAS_BAWAH 1 sampai 4 derajat, KULKAS_ATAS 1 sampai 4 derajat, FREEZER minus 18 derajat, SUHU_RUANG 25 sampai 28 derajat, BASKOM_BAWANG 25 sampai 30 derajat. Setiap batch menunjuk satu lokasi; pindah dicatat sebagai mutasi.
- BR-103 13 satuan hortikultura baku dikunci: `kg`, `g`, `ons`, `buah`, `butir`, `siung`, `ikat`, `batang`, `lembar`, `bonggol`, `pak`, `ml`, `liter`. Penyimpanan `kg` sebagai `gram` kali 1000, `liter` sebagai `ml` kali 1000. Satuan hitung pecahan ditolak; jumlah di atas 50.000 gram atau ml per batch ditolak dan dipecah.
- BR-104 Bobot acuan per komoditas dikunci untuk konversi hitung ke gram: bayam 1 ikat 200 gram, kangkung 1 ikat 200 gram, wortel 1 buah 100 gram, tomat 1 buah 80 gram, cabai merah 1 buah 5 gram, bawang merah 1 buah dan 1 siung 10 gram, bawang putih 1 siung 5 gram dan 1 bonggol 50 gram. Konversi tidak berlaku ditolak dengan pesan satuan tidak berlaku untuk komoditas ini.
- BR-105 Isi awal 12 bahan dikunci: beras 2.000 gram minimum 180 hari SUHU_RUANG, telur 6 pcs 14 hari KULKAS_BAWAH, bawang merah 250 gram 21 hari BASKOM_BAWANG, bawang putih 150 gram 30 hari BASKOM_BAWANG, cabai merah 100 gram 7 hari KULKAS_BAWAH, tomat 300 gram 5 hari KULKAS_BAWAH, bayam 1 ikat 2 hari KULKAS_BAWAH, kangkung 1 ikat 2 hari KULKAS_BAWAH, wortel 300 gram 14 hari KULKAS_BAWAH, minyak 500 ml 365 hari SUHU_RUANG, gula 500 gram 0 hari SUHU_RUANG, garam 250 gram 0 hari SUHU_RUANG.

## Metrik

- Komposisi stok per kategori, akurasi konversi 13 satuan, penolakan satuan tidak berlaku. Unik `householdId` plus nama kecil untuk item aktif.

## Batasan

Batasan segmen ini: hanya definisi kategori, lokasi, satuan, dan isi awal. Di luar batas: alur catat, kontrak API, dan implementasi aplikasi. Perubahan bobot acuan wajib lewat MR label pantry-resep dengan notifikasi ke Pawonee.
