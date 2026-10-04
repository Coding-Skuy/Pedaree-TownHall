> Versi: v1.0.0 | Status: disetujui | Menggantikan: -

# BRD 30 — Demand dan Metrik

## Konteks

Demand dan metrik mencakup sinyal ke TitipO dan ukuran hemat dapur. Sumber isi lama: `metrik/10-stockout-dan-waste.md` seluruhnya dan `inventori/10-model-stok-dapur.md` bab aturan hitung.

## Kebutuhan Bisnis

- BR-301 Stockout adalah tampil 0 untuk item aktif; menipis adalah lebih dari 0 sampai minimum. Sinyal demand dipicu pada transisi ke menipis dan ke habis, masing-masing maksimal 1 per item per 24 jam.
- BR-302 Waste adalah jumlah BUANG per bulan dalam gram, ml, pcs dikonversi via bobot acuan. WasteRupiah memakai harga parcel TitipO bila ada, bila tidak memakai acuan: beras Rp12.000 per kg, telur Rp2.500 per pcs, cabai Rp60.000 per kg, tomat Rp20.000 per kg, bawang merah Rp40.000 per kg, bawang putih Rp45.000 per kg, bayam dan kangkung Rp3.000 per ikat, wortel Rp18.000 per kg, minyak Rp22.000 per L, gula Rp18.000 per kg, garam Rp12.000 per kg.
- BR-303 Target bulan ke-3 dikunci: stockout harian rata-rata di bawah 8 persen, waste di bawah 1.500 gram dan Rp50.000 per rumah tangga per bulan, 60 persen sinyal MENIPIS berujung pesan TitipO 48 jam, 50 persen batch SEGERA dipakai.
- BR-304 Event analitik dikunci: `stok_menipis`, `stok_habis`, `buang_catat`, `sinyal_titipo`, `pakai_dari_resep` dengan properti item, sisa, minimum, alasan, resep. Periode default kalender bulanan WIB; dasbor web tampil bulan berjalan plus 2 bulan sebelumnya.
- BR-305 Setiap mutasi pemicu menerbitkan `pedaree.stok.v1` jenis `stok.menipis`, `stok.habis`, `kedaluwarsa.dekat` berisi item, sisa, satuan dari 13 baku, kedaluwarsa, dan `recipe_id` Pawonee bila ada. Pipeline meneruskan sebagai anjuran ke TitipO; keputusan akhir milik TitipO.
- BR-306 Tampilan dikunci: mobile 3 kartu ringkas habis hari ini, segera 3 hari, buang bulan ini; web dasbor grafik 90 hari, tabel waste per kategori, 10 item paling sering habis plus tombol naikkan minimum.

## Metrik

- Rumus stockoutRate 100 kali habis bagi total aktif harian 23:59 WIB, wasteGram, wasteRupiah, median jam menipis ke habis. Lembar harga acuan disimpan 6 bulan.

## Batasan

Batasan segmen ini: hanya demand, rumus, target, dan event. Di luar batas: logika penawaran TitipO dan skema resep Pawonee. Sinyal bersifat anjuran; TitipO memutuskan penawaran akhir.
