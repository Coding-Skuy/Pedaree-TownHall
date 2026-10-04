# 10 — Model Stok Dapur (Varian 1)

Status: disetujui Varian 1. Bahasa: Indonesia. Satuan lengkap lihat `inventori/20-satuan-hortikultura.md`.

## 1. Peran

Pedaree adalah sumber kebenaran stok dapur rumah tangga: apa yang ada, berapa banyak,
di mana disimpan, kapan kedaluwarsa, dan kapan harus beli lagi. Sinyal demand
(stok menipis / habis) dikirim ke TitipO dan Pasaree. Kebutuhan resep dibaca dari
modul bersama dengan Pawonee (`produk/30-modul-KMP-bersama.md`).

## 2. Entitas

### 2.1 Kategori

| Kode | Nama | Contoh |
|---|---|---|
| SAYUR | Sayuran | bayam, kangkung, wortel |
| BUMBU | Bumbu dapur | bawang merah, bawang putih, cabai |
| BUAH | Buahan | tomat, jeruk, pisang |
| PROTEIN | Protein hewani/nabati | telur, tempe, tahu, ayam |
| KERING | Bahan kering | beras, mie, tepung, gula, garam |
| CAIR | Cairan dapur | minyak, kecap, santan |
| BEKU | Bahan beku | ayam beku, bakso beku |
| SISA | Sisa masakan | sayur lodeh sisa, nasi sisa |

Kategori bersifat tetap Varian 1. Penambahan kategori baru memerlukan perubahan skema minor.

### 2.2 LokasiSimpan

| Kode | Nama | Suhu acuan |
|---|---|---|
| KULKAS_BAWAH | Rak bawah kulkas | 1–4 °C |
| KULKAS_ATAS | Chiller / rak atas | 1–4 °C |
| FREEZER | Freezer | −18 °C |
| SUHU_RUANG | Rak dapur suhu ruang | 25–28 °C |
| BASKOM_BAWANG | Keranjang bawang berventilasi | 25–30 °C |

Setiap batch wajib menunjuk satu lokasi. Pindah lokasi dicatat sebagai mutasi, bukan edit diam-diam
(lihat `produk/10-alur-catat-stok.md`).

### 2.3 PantryItem (kartu induk bahan)

Kartu induk per bahan per rumah tangga. Satu bahan = satu kartu.

| Field | Tipe | Wajib | Keterangan |
|---|---|---|---|
| id | string ULID | ya | contoh `itm_01J9Z8QK2M4A` |
| householdId | string ULID | ya | pemilik rumah tangga |
| nama | string 3–60 char | ya | contoh `Bawang Merah` |
| kategori | enum Kategori | ya | lihat tabel 2.1 |
| satuanSimpan | enum Satuan | ya | satuan kanonis penyimpanan, contoh `gram` |
| satuanTampil | enum Satuan | ya | satuan default di UI, contoh `gram` |
| stokMinimum | number ≥ 0 | ya | ambang sinyal menipis |
| umurSimpanHariDefault | int 0–365 | ya | 0 = tidak mudah busuk (garam, gula) |
| lokasiDefault | enum LokasiSimpan | ya | lokasi saat catat cepat |
| fotoUrl | string URL https / null | tidak | null bila belum ada foto |
| arsip | boolean | ya | false aktif, true diarsipkan |
| dibuatPada | RFC3339 UTC | ya | waktu buat |
| diubahPada | RFC3339 UTC | ya | waktu ubah terakhir |

Contoh:

```json
{
  "id": "itm_01J9Z8QK2M4A",
  "householdId": "hh_01J9Z8QK2M4B",
  "nama": "Bawang Merah",
  "kategori": "BUMBU",
  "satuanSimpan": "gram",
  "satuanTampil": "gram",
  "stokMinimum": 250,
  "umurSimpanHariDefault": 21,
  "lokasiDefault": "BASKOM_BAWANG",
  "fotoUrl": null,
  "arsip": false,
  "dibuatPada": "2026-09-01T02:00:00Z",
  "diubahPada": "2026-09-20T04:10:00Z"
}
```

### 2.4 StokBatch (unit kedaluwarsa)

Stok fisik selalu berbentuk batch. Stok total item = jumlah `jumlahAktif` seluruh batch aktif.
Prinsip pengeluaran: FEFO (yang paling cepat kedaluwarsa keluar lebih dulu).

| Field | Tipe | Wajib | Keterangan |
|---|---|---|---|
| id | string ULID | ya | contoh `btc_01J9Z8QK2M4C` |
| pantryItemId | string ULID | ya | relasi ke PantryItem |
| jumlahAwal | number > 0 | ya | dalam satuanSimpan |
| jumlahAktif | number ≥ 0 | ya | sisa yang masih bisa dipakai |
| tanggalMasuk | date YYYY-MM-DD | ya | tanggal fisik masuk dapur |
| tanggalKedaluwarsa | date / null | ya-null | null hanya bila umurSimpanHariDefault = 0 |
| lokasi | enum LokasiSimpan | ya | lokasi batch ini |
| sumber | enum MASUK_BELANJA, MASUK_PANEN, MASUK_TITIPO, MASUK_KOREKSI | ya | asal batch |
| status | enum AKTIF, HABIS, KEDALUWARSA, DIBUANG | ya | status turunan |
| dibuatPada | RFC3339 UTC | ya | waktu catat |

Aturan turunan status:

- `HABIS` bila `jumlahAktif = 0` dan belum melewati tanggal kedaluwarsa.
- `KEDALUWARSA` bila hari ini > tanggalKedaluwarsa dan `jumlahAktif > 0` (jumlah tetap dicatat untuk metrik waste).
- `DIBUANG` hanya lewat aksi buang eksplisit.

Contoh:

```json
{
  "id": "btc_01J9Z8QK2M4C",
  "pantryItemId": "itm_01J9Z8QK2M4A",
  "jumlahAwal": 1000,
  "jumlahAktif": 650,
  "tanggalMasuk": "2026-09-28",
  "tanggalKedaluwarsa": "2026-10-19",
  "lokasi": "BASKOM_BAWANG",
  "sumber": "MASUK_BELANJA",
  "status": "AKTIF",
  "dibuatPada": "2026-09-28T09:15:00Z"
}
```

### 2.5 MutasiStok (buku kas)

Setiap perubahan jumlahAktif wajib menulis satu baris mutasi. Tidak ada perubahan stok tanpa mutasi.

| Field | Tipe | Keterangan |
|---|---|---|
| id | ULID | kunci baris |
| batchId | ULID | batch yang berubah |
| jenis | PAKAI, MASUK, KOREKSI, PINDAH, BUANG | jenis kejadian |
| jumlah | number (positif masuk, negatif keluar) | dalam satuanSimpan |
| sisaSetelah | number ≥ 0 | jumlahAktif batch setelah kejadian |
| alasan | string 3–140 char | alasan bebas, wajib untuk KOREKSI dan BUANG |
| aktor | string userId | pencatat |
| terjadiPada | RFC3339 UTC | waktu kejadian |

## 3. Aturan Hitung

1. Stok tampil per item = Σ `jumlahAktif` batch berstatus AKTIF, dibulatkan ke 1 desimal untuk gram/ml dan ke bilangan bulat untuk pcs/siung/buah/ikat/batang/bonggol/lembar.
2. Konversi antar satuan memakai tabel `inventori/20-satuan-hortikultura.md`. Penyimpanan selalu dikonversi ke satuanSimpan sebelum dijumlah.
3. Stok menipis = stok tampil ≤ stokMinimum dan > 0. Stok habis = stok tampil = 0.
4. Sinyal demand ke TitipO dipicu pada transisi ke menipis dan ke habis, masing-masing maksimal 1 sinyal per item per 24 jam (debounce di sisi server).
5. Pengeluaran PAKAI selalu mengambil dari batch dengan tanggalKedaluwarsa paling dekat lebih dulu (FEFO). Bila tanggalKedaluwarsa null, batch tertua keluar dulu (FIFO).

## 4. Indeks dan Batas

- Unik: `(householdId, lower(nama))` untuk PantryItem aktif (arsip = false).
- Maksimum 400 PantryItem aktif per rumah tangga dan 2.000 batch per rumah tangga Varian 1.
- Retensi MutasiStok 365 hari. Data lebih lama diagregasi harian.

## 5. Contoh Varian 1 (isi awal 12 bahan)

| Nama | Satuan simpan | Minimum | Umur (hari) | Lokasi default |
|---|---|---|---|---|
| Beras | gram | 2000 | 180 | SUHU_RUANG |
| Telur Ayam | pcs | 6 | 14 | KULKAS_BAWAH |
| Bawang Merah | gram | 250 | 21 | BASKOM_BAWANG |
| Bawang Putih | gram | 150 | 30 | BASKOM_BAWANG |
| Cabai Merah | gram | 100 | 7 | KULKAS_BAWAH |
| Tomat | gram | 300 | 5 | KULKAS_BAWAH |
| Bayam | ikat | 1 | 2 | KULKAS_BAWAH |
| Kangkung | ikat | 1 | 2 | KULKAS_BAWAH |
| Wortel | gram | 300 | 14 | KULKAS_BAWAH |
| Minyak Goreng | ml | 500 | 365 | SUHU_RUANG |
| Gula Pasir | gram | 500 | 0 | SUHU_RUANG |
| Garam | gram | 250 | 0 | SUHU_RUANG |
