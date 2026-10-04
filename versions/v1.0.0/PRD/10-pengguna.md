> Versi: v1.0.0 | Status: disetujui | Menggantikan: -

# PRD 10 — Pengguna Pedaree

## Daftar Peran

- Penghuni dapur: mencatat masuk dan pakai harian kurang dari 30 detik, melihat expiry FEFO, menerima push WASPADA dan SEGERA dan HARI_INI, dan meneruskan kurang ke TitipO. Kebutuhan: daftar dapur, detail item, pratinjau FEFO.
- Admin rumah tangga: mengelola kartu bahan, minimum, lokasi default, opname massal, impor dan ekspor CSV, dan dasbor stockout dan waste. Kebutuhan: tabel opname, impor 200 baris, ekspor stok dan mutasi.
- TitipO penerima sinyal: menerima sinyal MENIPIS dan HABIS via `pedaree.stok.v1` lewat pipeline sebagai anjuran titip belanja. Kebutuhan: item, sisa, satuan 13 baku, kedaluwarsa, `recipe_id` Pawonee.
- Pawonee pemilik resep: memiliki modul `:shared:pantry-resep` 1.0.0 dan definisi resep; Pedaree hanya KONSUMEN yang membaca skor kecocokan dan tombol Masak ini. Kebutuhan: stok Pedaree per bahan dan sinyal kurang.

## Hak Akses

- Satu rumah tangga satu ruang data: `X-Household-Id` wajib tiap request, server memfilter per id. Penghuni hanya datanya sendiri. Admin rumah tangga melihat seluruh item rumah tangganya. TitipO hanya menerima sinyal yang diteruskan pipeline, tidak membaca DB `pedaree` langsung untuk pemicu utama.
- Autentikasi: JWT dengan audien `pedaree`, mobile Bearer 30 hari plus `X-Client kmp-mobile per 1.0.0`, web cookie sesi `pedaree_sess` httpOnly. `X-Request-Id` UUID per tulis untuk idempotensi 24 jam. Tidak ada akun bersama: 1 orang 1 akun.

## Batasan

Batasan dokumen ini: hanya peran, kebutuhan pandang, dan hak akses. Aturan bisnis rinci ada di BRD, langkah sistem ada di FSD. Di luar batas: peran kurir TitipO dan kasir pasar yang diatur TownHall masing-masing.
