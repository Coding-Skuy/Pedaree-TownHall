# 20 — Satuan Hortikultura (Varian 1)

Status: disetujui Varian 1. Bahasa: Indonesia.

## 1. Prinsip

- Setiap `PantryItem` menyimpan stok dalam satu `satuanSimpan` kanonis.
- UI boleh menampilkan `satuanTampil` yang berbeda; konversi memakai tabel di dokumen ini.
- Semua konversi `ikat`, `siung`, `buah`, `batang`, `bonggol`, `lembar` memakai bobot acuan
  per komoditas pada tabel 4. Tidak ada konversi umum tanpa menyebut komoditas.

## 2. Daftar Satuan Sah Varian 1

| Kode | Jenis | Simbol tampil | Ketelitian simpan |
|---|---|---|---|
| gram | berat | g | 0,1 g |
| kg | berat | kg | 0,0001 kg (disimpan sebagai gram) |
| ml | volume | ml | 0,1 ml |
| liter | volume | L | 0,0001 L (disimpan sebagai ml) |
| sdm | volume | sdm | 1 sdm = 15 ml |
| sdt | volume | sdt | 1 sdt = 5 ml |
| pcs | hitung | pcs | bilangan bulat |
| siung | hitung | siung | bilangan bulat |
| buah | hitung | buah | bilangan bulat |
| ikat | hitung | ikat | bilangan bulat |
| batang | hitung | btg | bilangan bulat |
| bonggol | hitung | bonggol | bilangan bulat |
| lembar | hitung | lbr | bilangan bulat |

Satuan `kg` selalu disimpan sebagai `gram` (×1000). Satuan `liter`, `sdm`, `sdt`
selalu disimpan sebagai `ml` (×1000, ×15, ×5).

## 3. Aturan Tampil dan Pembulatan

1. Berat/volume tampil dibulatkan 1 desimal bila < 1000, tanpa desimal bila ≥ 1000
   dengan pemisah ribuan titik (contoh `1.500 g`).
2. Satuan hitung tampil selalu bilangan bulat. Sisa pecahan hasil konversi
   (misalnya 0,4 ikat) ditampilkan sebagai satuan berat (`350 g`) bila bobot acuan tersedia.
3. Input pengguna menerima koma maupun titik desimal. Server menormalkan ke titik.
4. Pembulatan penyimpanan: gram dan ml ke 1 desimal dengan pembulatan setengah ke atas.

## 4. Bobot Acuan per Komoditas (1 satuan hitung = bobot gram)

Dipakai saat `satuanTampil` berupa hitung tetapi `satuanSimpan` berupa gram, atau sebaliknya.

| Komoditas | 1 ikat | 1 buah | 1 siung | 1 batang | 1 bonggol | 1 lembar |
|---|---|---|---|---|---|---|
| Bayam | 200 g | — | — | — | — | — |
| Kangkung | 200 g | — | — | — | — | — |
| Kemangi | 50 g | — | — | — | — | — |
| Daun Bawang | — | — | — | 25 g | — | — |
| Sawi Hijau | — | — | — | — | 300 g | — |
| Kol | — | — | — | — | 800 g | — |
| Wortel | — | 100 g | — | — | — | — |
| Kentang | — | 150 g | — | — | — | — |
| Tomat | — | 80 g | — | — | — | — |
| Cabai Merah | — | 5 g | — | — | — | — |
| Bawang Merah | — | 10 g | 10 g | — | — | — |
| Bawang Putih | — | — | 5 g | — | 50 g | — |
| Jagung | — | 250 g | — | — | — | — |

Tanda — berarti konversi itu tidak berlaku untuk komoditas tersebut dan input ditolak
dengan pesan `satuan tidak berlaku untuk komoditas ini`.

## 5. Contoh Konversi

- 2 ikat bayam → disimpan `400 g` (2 × 200 g).
- 500 g bawang merah → tampil `50 siung` (500 ÷ 10), stok tampil tetap `500 g`.
- 3 sdm minyak → disimpan `45 ml` (3 × 15).
- 1 kg beras → disimpan `1000 g`.
- 6 buah tomat → disimpan `480 g` (6 × 80).

## 6. Validasi Input Varian 1

- Jumlah ≤ 0 ditolak.
- Jumlah > 50.000 g atau > 50.000 ml dalam satu batch ditolak (pecah menjadi beberapa batch).
- Satuan hitung pecahan (misalnya `1,5 ikat`) ditolak; pengguna wajib memasukkan gram atau ikat bulat.
- Komoditas di luar tabel 4 hanya boleh memakai gram, kg, ml, liter, sdm, sdt, pcs.
