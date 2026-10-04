> Versi: v1.0.0 | Status: disetujui | Menggantikan: -

# MILESTONE Pedaree — Status Living

Legenda status: todo berarti belum mulai, doing berarti sedang berjalan, done berarti selesai terverifikasi.

## Milestone v1.0.0

- M-001 Model PantryItem, StokBatch, MutasiStok terkunci dengan FEFO — status: done. Bukti: `versions/v1.0.0/FSD/20-model-data.md` dan uji FEFO 12 bahan lolos.
- M-002 13 satuan hortikultura baku terkunci dan validasi menolak di luar daftar — status: done. Bukti: `versions/v1.0.0/BRD/10-inventori.md` dan uji konversi 13 satuan lolos.
- M-003 Catat masuk dan catat pakai FEFO berjalan luring penuh — status: doing. Target: 3 dari 4 minggu sinkron kurang dari 60 detik setelah daring dan nol kehilangan mutasi.
- M-004 Job expiry-sweep 06:00 WIB dan push H-7, H-3, H-1 berjalan — status: doing. Bukti: log job 7 hari dan push tepat kanal mobile.
- M-005 Event `pedaree.stok.v1` terbit tiap mutasi dan sinyal MENIPIS dan HABIS sampai TitipO — status: doing. Bukti: offset pipeline dan status TERKIRIM di `GET /demand/signals`.
- M-006 Dasbor stockout dan waste tampil di web — status: todo. Syarat mulai: 30 rumah tangga mencatat 14 hari berturut-turut.
- M-007 Modul `:shared:pantry-resep` 1.0.0 dikonsumsi tanpa fork — status: doing. Bukti: build KMP dan web memakai `recipe_id` Pawonee tanpa duplikasi skema.
- M-008 Audit sinyal pertama dan 100 rumah tangga aktif — status: todo. Syarat lulus pilot: stockout rata-rata di bawah 8 persen, waste di bawah 1.500 gram, 60 persen sinyal berujung pesan TitipO 48 jam.

## Aturan Pembaruan

- Status diubah hanya oleh Kepala Pantry dengan bukti tanggal. Milestone yang sudah done tidak dihapus, hanya ditambah catatan verifikasi.

## Batasan

Batasan dokumen ini: hanya status milestone dan bukti ringkas. Rincian angka ada di `versions/v1.0.0/BRD/` dan `versions/v1.0.0/PRD/30-kriteria.md`. Dokumen ini tidak mengubah janji beku v1.0.0.
