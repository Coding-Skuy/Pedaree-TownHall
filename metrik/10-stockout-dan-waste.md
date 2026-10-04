# 10 — Stockout dan Waste (Varian 1)

Status: disetujui Varian 1. Bahasa: Indonesia.

## 1. Definisi

- **Stockout (habis)**: `stokTampil = 0` untuk item yang tidak diarsip.
- **Menipis**: `0 < stokTampil ≤ stokMinimum`.
- **Waste (buangan)**: jumlah `BUANG` dalam gram/ml/pcs pada periode berjalan,
  termasuk batch KEDALUWARSA yang dibuang.
- Periode default: kalender bulanan WIB. Dasbor web menampilkan bulan berjalan
  + 2 bulan sebelumnya.

## 2. Rumus

1. `stockoutRate = 100 × (item HABIS ÷ total item aktif)`, dihitung harian pukul 23:59 WIB.
2. `wasteGram = Σ jumlah BUANG (dikonversi ke gram/ml)` per bulan.
   Satuan hitung dikonversi memakai bobot `inventori/20-satuan-hortikultura.md`.
3. `wasteRupiah = Σ (jumlahBuang ÷ jumlahBeliAcuan × hargaBeli)` bila harga beli
   tercatat di parcel TitipO; bila tidak tercatat, baris itu memakai harga acuan
   Varian 1: beras Rp12.000/kg, telur Rp2.500/pcs, cabai Rp60.000/kg,
   tomat Rp20.000/kg, bawang merah Rp40.000/kg, bawang putih Rp45.000/kg,
   bayam/kangkung Rp3.000/ikat, wortel Rp18.000/kg, minyak Rp22.000/L,
   gula Rp18.000/kg, garam Rp12.000/kg.
4. `waktuMenipisKeHabis = median jam dari MENIPIS ke HABIS` per item per bulan.

## 3. Target Varian 1 (angka terkunci)

| Metrik | Target bulan ke-3 |
|---|---|
| stockoutRate harian rata-rata | ≤ 8% |
| wasteGram per rumah tangga per bulan | ≤ 1.500 g |
| wasteRupiah per rumah tangga per bulan | ≤ Rp50.000 |
| % sinyal MENIPIS yang berujung pesan TitipO ≤ 48 jam | ≥ 60% |
| % batch SEGERA yang dipakai (bukan dibuang) | ≥ 50% |

## 4. Event Analitik

| Event | Kapan dikirim | Properti |
|---|---|---|
| `stok_menipis` | transisi ke MENIPIS | itemId, sisa, minimum |
| `stok_habis` | transisi ke HABIS | itemId, jamSejakMenipis |
| `buang_catat` | mutasi BUANG | batchId, jumlah, alasan |
| `sinyal_titipo` | sinyal ke TitipO | itemId, jenis, statusKirim |
| `pakai_dari_resep` | Catat Pakai via Pawonee | resepId, itemIds |

## 5. Tampilan

- Mobile: 3 kartu ringkas (Habis hari ini, Segera 3 hari, Buang bulan ini).
- Web `/dasbor`: grafik garis stockoutRate 90 hari, tabel waste per kategori,
  daftar 10 item paling sering habis + tombol `Naikkan minimum`.
