> Versi: v1.0.0 | Status: disetujui | Menggantikan: -

# PRD 20 — Alur Produk

## Alur Catat Stok

1. Alur A catat masuk: tekan tambah catat masuk, pilih 12 bahan atau kartu baru, isi jumlah dan satuan 13 baku dikonversi otomatis, tanggal masuk default hari ini boleh mundur 7 hari, kedaluwarsa default masuk plus umur boleh ubah, lokasi default. Sistem membuat 1 StokBatch sumber MASUK_BELANJA dan MASUK_PANEN dan MASUK_TITIPO plus 1 mutasi MASUK. Parcel TitipO DITERIMA mengusulkan draf satu klik.
2. Alur B catat pakai: tekan catat pakai dari daftar atau rekomendasi Pawonee tombol Masak ini, isi jumlah per bahan, tampil pratinjau FEFO. Sistem memotong jumlahAktif FEFO satu per satu, tulis 1 mutasi PAKAI per batch tersentuh. Bila kurang tampil kurang 200 gram tomat dengan tombol tambah ke TitipO.
3. Alur C opname: buka opname tampil stok catat, masukkan fisik, selisih otomatis mutasi KOREKSI alasan wajib. Negatif ambil FEFO; positif buat batch MASUK_KOREKSI kedaluwarsa hari ini plus umur default.
4. Alur D pindah lokasi: pilih batch tekan pindah pilih tujuan, tulis mutasi PINDAH jumlah 0 lokasi lama ke baru di alasan, perbarui lokasi batch.
5. Alur E buang: tekan buang dari banner atau detail, isi jumlah default seluruh sisa plus alasan baku, tulis BUANG dan status DIBUANG bila sisa 0.

## Contoh Nyata

Rumah tangga mencatat masuk Jumat 09:15: 1.000 gram bawang merah BASKOM_BAWANG kedaluwarsa 19 Okt 2026. Memasak Sabtu memakai 350 gram: sistem memotong batch terdekat FEFO, sisa 650 gram. Stok tampil 650 gram di atas minimum 250 gram sehingga AMAN. Memasak Senin 500 gram: sisa 150 gram di bawah minimum sehingga transisi MENIPIS menerbitkan `pedaree.stok.v1` dan tombol tambah ke TitipO muncul.

## Batasan

Batasan alur ini: hanya masuk, pakai, opname, pindah, dan buang. Di luar batas: pengolahan resep Pawonee dan penawaran TitipO. Setiap alur selesai maksimal 3 layar mobile dan 2 langkah web; tombol simpan tampil ringkasan konversi 2 ikat sama dengan 400 gram sebelum disimpan.
