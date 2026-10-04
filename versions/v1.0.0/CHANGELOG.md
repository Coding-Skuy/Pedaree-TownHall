> Versi: v1.0.0 | Status: disetujui | Menggantikan: -

# CHANGELOG v1.0.0 — Versi Awal Pedaree

## Ringkasan Isi

v1.0.0 adalah versi awal TownHall Pedaree yang dibekukan mengikuti template emas Lumbung. Seluruh isi lama dari folder `inventori/`, `notifikasi/`, `produk/`, `platform/`, dan `metrik/` dipecah dan dipindah dengan `git mv` ke struktur versi ini, lalu folder lama dihapus agar hanya ada satu sumber kebenaran.

## Isi per Direktori

- BRD: `00-ikhtisar.md` memuat piagam Smart Pantry, FEFO, 13 satuan hortikultura, event `pedaree.stok.v1` ke TitipO, DB `pedaree`, JWT audien `pedaree`, dan status KONSUMEN modul milik Pawonee. `10-inventori.md` memuat kategori, lokasi, 13 satuan, dan 12 bahan awal. `20-kedaluwarsa.md` memuat ambang H-7, H-3, H-1, job 06:00 WIB, dan aksi wajib. `30-demand-metrik.md` memuat rumus stockout dan waste, target bulan ke-3, dan event analitik.
- PRD: `10-pengguna.md` memuat 4 peran: penghuni dapur, admin rumah tangga, TitipO penerima sinyal, Pawonee pemilik resep. `20-alur.md` memuat 5 alur masuk, pakai, opname, pindah, buang. `30-kriteria.md` memuat US-001 dan seterusnya, acceptance, dan non-goals.
- FRD: `10-fungsional.md` memuat FR-001 dan seterusnya per segmen inventori, notifikasi, demand, tanpa cara implementasi.
- FSD: `10-alur.md` memuat urutan tulis lokal dulu, antre FIFO, idempotensi, konflik 409. `20-model-data.md` memuat entitas PantryItem, StokBatch, MutasiStok. `30-kontrak.md` memuat kontrak API mobile dan web, event `pedaree.stok.v1`, error, dan autentikasi JWT audien `pedaree`.
- `SNAPSHOT-ROADMAP.md` memuat salinan beku janji pilot 90 hari.

## Sumber Pemindahan

- `inventori/10-model-stok-dapur.md` menjadi FSD model data dan BRD ikhtisar. `inventori/20-satuan-hortikultura.md` menjadi BRD inventori.
- `notifikasi/10-sop-kedaluwarsa.md` menjadi BRD kedaluwarsa. `metrik/10-stockout-dan-waste.md` menjadi BRD demand dan PRD kriteria.
- `produk/10-alur-catat-stok.md` menjadi PRD alur. `produk/20-kontrak-api-KMP-mobile.md` dan `produk/21-kontrak-api-web-bun.md` menjadi FSD kontrak. `produk/30-modul-KMP-bersama.md` menjadi FSD kontrak bagian modul KONSUMEN.
- `platform/10-matriks-KMP-web.md`, `platform/20-navigasi3.md`, `platform/40-mobile-KMP.md`, `platform/50-web-bun-svelte.md`, `platform/60-offline-sinkron.md` menjadi FSD alur dan kontrak.

## Batasan

Batasan versi ini: hanya stok dapur rumah tangga dengan FEFO, 13 satuan hortikultura baku, expiry H-7, H-3, H-1, sinyal demand via `pedaree.stok.v1` ke TitipO, DB `pedaree`, JWT audien `pedaree`. Perubahan setelah ini wajib masuk v1.1.0 atau v2.0.0 dan dicatat di `roadmap/` living, bukan dengan mengubah file beku ini.
