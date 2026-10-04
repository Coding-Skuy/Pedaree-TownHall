> Versi: v1.0.0 | Status: disetujui | Menggantikan: -

# ROADMAP Pedaree — Rencana ke Depan

Dokumen living. Janji v1.0.0 yang sudah beku tidak diubah di sini; perubahan masa depan dirilis sebagai versi baru.

## v1.1.0 — Penguatan Sinyal dan Akurasi

- Tujuan: menaikkan sinyal MENIPIS yang berujung pesan TitipO dalam 48 jam ke atas 70 persen dan menekan stockout harian di bawah 6 persen.
- Isi rencana: debounce sinyal tetap 24 jam per item, agregasi sinyal per rumah tangga untuk TitipO, impor CSV 500 baris, opname massal web dengan pratinjau FEFO.
- Prasyarat rilis: M-003 dan M-005 berstatus done selama 4 minggu berturut-turut.

## v2.0.0 — Skala Rumah Tangga dan Resep

- Tujuan: dukung 1.000 rumah tangga aktif dengan retensi mutasi 365 hari dan paket rekomendasi hemat berbasis sisa FEFO.
- Isi rencana: partisi DB `pedaree` per household, cache dasbor 60 detik menjadi 15 detik, bonus bahan SEGERA di skor Pawonee naik review, ekspor mutasi audit 1 tahun.
- Prasyarat rilis: pilot 90 hari lulus penuh dan audit ketepatan sinyal bersih.

## Non-Tujuan Roadmap

- Tidak memutuskan pembelian atau penawaran TitipO; Pedaree hanya mengirim sinyal anjuran via `pedaree.stok.v1`.
- Tidak menduplikasi definisi resep Pawonee; Pedaree tetap KONSUMEN modul `:shared:pantry-resep` milik Pawonee.

## Batasan

Batasan dokumen ini: hanya arah rencana dan prasyarat versi. Keputusan bisnis rinci tiap versi masa depan wajib ditulis ulang di `versions/vX.Y.Z/` masing-masing. Dokumen ini tidak menjadi kontrak API atau acuan pembayaran.
