> Versi: v1.0.0 | Status: disetujui | Menggantikan: -

# FSD 20 — Model Data

## Entitas Inti

- PantryItem: id ULID contoh `itm_01J9Z8QK2M4A`, householdId ULID, nama 3 sampai 60 char, kategori enum SAYUR dan BUMBU dan BUAH dan PROTEIN dan KERING dan CAIR dan BEKU dan SISA, satuanSimpan dan satuanTampil dari 13 baku, stokMinimum number, umurSimpanHariDefault 0 sampai 365, lokasiDefault enum, fotoUrl https dan null, arsip boolean, dibuatPada dan diubahPada RFC3339 UTC. Contoh bawang merah gram 250 minimum 21 hari BASKOM_BAWANG.
- StokBatch: id ULID, pantryItemId, jumlahAwal lebih dari 0, jumlahAktif, tanggalMasuk YYYY-MM-DD, tanggalKedaluwarsa date dan null hanya umur 0, lokasi enum, sumber enum MASUK_BELANJA dan MASUK_PANEN dan MASUK_TITIPO dan MASUK_KOREKSI, status enum AKTIF dan HABIS dan KEDALUWARSA dan DIBUANG. Contoh 1.000 gram masuk 28 Sep 2026 kedaluwarsa 19 Okt 2026 sisa 650 gram AKTIF. HABIS bila 0 belum lewat tanggal; KEDALUWARSA bila lewat tanggal dan sisa lebih dari 0; DIBUANG hanya aksi eksplisit.
- MutasiStok: id ULID, batchId, jenis PAKAI dan MASUK dan KOREKSI dan PINDAH dan BUANG, jumlah positif masuk negatif keluar dalam satuanSimpan, sisaSetelah, alasan 3 sampai 140 char wajib KOREKSI dan BUANG, aktor userId, terjadiPada RFC3339 UTC. Tidak ada ubah stok tanpa mutasi.
- Entitas pendukung: Household, SinyalDemand MENIPIS dan HABIS, ParcelTitipO DITERIMA, AntreTulis requestId dan method dan path dan bodyJson.

## Aturan Angka

- Berat selalu gram 1 desimal dan ml 1 desimal, hitung pcs dan siung dan buah dan ikat dan batang dan bonggol dan lembar bilangan bulat. `kg` disimpan gram, `liter` dan `sdm` dan `sdt` disimpan ml. Uang rupiah integer. Stok tampil jumlah AKTIF dibulatkan 1 desimal gram dan ml.
- Retensi: MutasiStok 365 hari lalu agregasi harian; foto 500 KB 1 tahun lalu metadata. Maksimum 400 item aktif dan 2.000 batch per rumah tangga. Unik householdId plus nama kecil item aktif. DB `pedaree` Postgres adalah otoritatif; klien hanya tembolok.

## Batasan

Batasan dokumen ini: hanya definisi entitas, kunci, enum, dan aturan angka. Serialisasi JSON dan endpoint ada di `30-kontrak.md`. Perubahan skema butuh persetujuan Tech Lead dan migrasi teruji.
