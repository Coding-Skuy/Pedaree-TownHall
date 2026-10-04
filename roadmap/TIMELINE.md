> Versi: v1.0.0 | Status: disetujui | Menggantikan: -

# TIMELINE Pedaree — Garis Waktu Hidup Lintas Versi

Dokumen living: diperbarui tiap ada versi baru. Salinan beku v1.0.0 ada di `versions/v1.0.0/SNAPSHOT-ROADMAP.md` dan tidak diubah lagi.

## Garis Waktu

- 26 Sep 2026 sampai 09 Okt 2026 — Persiapan pantry. Kunci 12 bahan awal, 13 satuan hortikultura baku, kategori dan lokasi simpan, FEFO dan FIFO cadangan. Sumber isi lama: `inventori/10-model-stok-dapur.md` dan `inventori/20-satuan-hortikultura.md`.
- 10 Okt 2026 — v1.0.0 disetujui. Struktur versi BRD, PRD, FRD, FSD dibekukan mengikuti template emas Lumbung. Kunci: FEFO, 13 satuan, event `pedaree.stok.v1` ke TitipO, DB `pedaree`, JWT audien `pedaree`, KONSUMEN `:shared:pantry-resep` milik Pawonee.
- Hari 1 sampai 30 pilot — Operasi kecil. 30 rumah tangga, catat masuk dan pakai harian, expiry-sweep 06:00 WIB, push H-7, H-3, H-1. Target arus: catat masuk kurang dari 30 detik, sinkron luring kurang dari 60 detik.
- Hari 31 sampai 60 pilot — Sinyal penuh. Semua transisi MENIPIS dan HABIS menerbitkan `pedaree.stok.v1`, debounce 24 jam, dasbor web stockout dan waste tampil, opname massal CSV 200 baris.
- Hari 61 sampai 90 pilot — Kesiapan lepas pilot. Genap 100 rumah tangga. Audit sinyal pertama. Syarat lulus: stockout di bawah 8 persen, waste di bawah 1.500 gram dan Rp50.000, 60 persen sinyal berujung pesan TitipO 48 jam, 50 persen batch SEGERA dipakai bukan dibuang.
- Setelah pilot — Skala dan versi berikutnya. Agregasi sinyal per rumah tangga, impor 500 baris, partisi DB `pedaree`, dan paket hemat FEFO. Rencana rinci menunjuk `ROADMAP.md` untuk v1.1.0 dan v2.0.0.

## Keterkaitan Versi

- v1.0.0 menjadi acuan awal. Perubahan jadwal pada versi baru dicatat di sini dengan tanggal dan nomor versi, tanpa mengubah snapshot beku.

## Batasan

Batasan dokumen ini: hanya mencatat tonggak waktu dan fase. Detail kebutuhan tetap di `versions/v1.0.0/BRD/`, detail kriteria lulus di `versions/v1.0.0/PRD/30-kriteria.md`, dan detail janji beku di `SNAPSHOT-ROADMAP.md`. Dokumen ini tidak mengatur tarif, grade, atau kontrak API.
