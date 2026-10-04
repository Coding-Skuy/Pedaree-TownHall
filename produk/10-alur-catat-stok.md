# 10 — Alur Catat Stok (Varian 1)

Status: disetujui Varian 1. Bahasa: Indonesia.

Lima alur resmi. Semua alur menulis `MutasiStok` (lihat `inventori/10-model-stok-dapur.md`).

## Alur A — Catat Masuk (belanja / panen / TitipO tiba)

Dipakai saat bahan fisik masuk dapur.

1. Pengguna menekan `+ Catat masuk`, memilih bahan dari 12 bahan baku
   (atau membuat kartu baru dengan nama, kategori, satuan, minimum, umur, lokasi).
2. Pengguna mengisi: jumlah + satuan (dikonversi otomatis, lihat `inventori/20-satuan-hortikultura.md`),
   tanggal masuk (default hari ini, boleh mundur maksimal 7 hari),
   tanggal kedaluwarsa (default = tanggal masuk + umurSimpanHariDefault, boleh diubah),
   lokasi (default lokasiDefault).
3. Sistem membuat 1 `StokBatch` baru (sumber MASUK_BELANJA, MASUK_PANEN, atau MASUK_TITIPO)
   dan 1 mutasi MASUK.
4. Parcel TitipO yang ditandai diterima otomatis mengusulkan draf Catat Masuk berisi
   isi parcel; pengguna cukup menekan `Simpan` atau mengoreksi jumlah.

Validasi: jumlah > 0, tanggal masuk tidak di masa depan, tanggal kedaluwarsa ≥ tanggal masuk
(kecuali umur 0 yang memakai null).

## Alur B — Catat Pakai (memasak / makan langsung)

1. Pengguna menekan `Catat pakai` dari daftar dapur atau dari rekomendasi Pawonee
   (tombol `Masak ini` mengirim daftar bahan + jumlah ke Pedaree).
2. Pengguna mengisi jumlah per bahan. Sistem menampilkan pratinjau batch FEFO
   yang akan terpotong.
3. Sistem mengurangi `jumlahAktif` batch FEFO satu per satu sampai kebutuhan terpenuhi,
   menulis 1 mutasi PAKAI per batch yang tersentuh.
4. Bila stok kurang dari kebutuhan, sistem menampilkan kekurangan (`kurang 200 g tomat`)
   dengan tombol `Tambah ke TitipO` yang meneruskan sinyal demand.

Validasi: batch KEDALUWARSA tidak bisa dipakai; stok 0 menolak dengan pesan `stok habis`.

## Alur C — Opname / Koreksi (stok fisik beda dengan catat)

1. Pengguna membuka `Opname`, sistem menampilkan stok catat per bahan.
2. Pengguna memasukkan stok fisik hasil timbang/hitung.
3. Selisih otomatis dibuatkan mutasi KOREKSI dengan alasan wajib (contoh `tumpah`,
   `susut bayam`, `salah takar kemarin`).
4. Koreksi negatif mengambil dari batch FEFO; koreksi positif membuat batch baru
   bersumber MASUK_KOREKSI dengan tanggal kedaluwarsa = hari ini + umur default.

## Alur D — Pindah Lokasi

1. Pengguna memilih batch, menekan `Pindah`, memilih lokasi tujuan.
2. Sistem menulis mutasi PINDAH (jumlah 0, lokasi lama → baru di kolom alasan)
   dan memperbarui field lokasi batch.
3. Riwayat pindah tampil di detail batch.

## Alur E — Buang Kedaluwarsa / Rusak

1. Pengguna menekan `Buang` dari banner kedaluwarsa atau detail batch.
2. Pengguna mengisi jumlah buang (default seluruh sisa) + alasan dari daftar baku
   (`busuk`, `berlendir/berbau`, `lewat tanggal`, `tercemar`).
3. Sistem menulis mutasi BUANG dan mengubah status menjadi DIBUANG bila sisa 0.

## Aturan Umum Semua Alur

- Setiap alur selesai dalam maksimal 3 layar di mobile dan 2 langkah di web.
- Tombol simpan selalu menampilkan ringkasan konversi (`2 ikat = 400 g`) sebelum disimpan.
- Semua alur bisa dilakukan luring; antrean sinkron dijelaskan di `platform/60-offline-sinkron.md`.
- Setiap alur yang membuat stok ≤ minimum atau = 0 memicu sinyal demand sesuai
  aturan debounce di `inventori/10-model-stok-dapur.md` bab 3.
