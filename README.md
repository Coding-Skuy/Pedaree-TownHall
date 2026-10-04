# Pedaree-TownHall — Smart Pantry: Stok Dapur, Expiry, Sinyal Demand

Bahasa: Indonesia. Varian: 1 (seluruh keputusan terkunci, tanpa placeholder).

## 1. Peran

Pedaree adalah sumber kebenaran stok dapur: mencatat apa yang ada, mengingatkan
kapan kedaluwarsa, dan mengirim sinyal demand saat stok menipis atau habis.

- Masuk: belanja manual, panen, parcel TitipO yang diterima.
- Keluar: dipakai memasak (termasuk via rekomendasi Pawonee), opname, buang.
- Sinyal keluar: demand MENIPIS/HABIS ke TitipO dan Pasaree; kebutuhan resep ke Pawonee
  lewat modul bersama.

TownHall ini hanya berisi keputusan dan kontrak. Kode hidup di repo aplikasi
masing-masing (mobile KMP dan web Bun).

## 2. Peta Folder

```
inventori/
  10-model-stok-dapur.md      → entitas PantryItem, StokBatch, MutasiStok, aturan FEFO
  20-satuan-hortikultura.md   → satuan sah + bobot acuan 12 komoditas
notifikasi/
  10-sop-kedaluwarsa.md       → ambang H-7/H-3/H-1, template push, aksi wajib
produk/
  10-alur-catat-stok.md       → 5 alur: masuk, pakai, opname, pindah, buang
  20-kontrak-api-KMP-mobile.md → REST /v1 untuk klien KMP (Kotlin models)
  21-kontrak-api-web-bun.md    → REST /v1 + /web untuk SvelteKit (TS types, CSV, dasbor)
  30-modul-KMP-bersama.md     → modul :shared:pantry-resep 1.0.0 bersama Pawonee
platform/
  10-matriks-KMP-web.md       → pembagian kerja mobile vs web
  20-navigasi3.md             → graf Navigation3 + NavKey + deep link
  40-mobile-KMP.md            → versi Kotlin/Compose/Ktor/SQLDelight + struktur modul
  50-web-bun-svelte.md        → versi Bun/SvelteKit/TS + struktur routes
  60-offline-sinkron.md       → antre FIFO, idempotensi, konflik opname 409
metrik/
  10-stockout-dan-waste.md    → rumus stockoutRate, wasteGram/Rupiah, target, event
```

Urutan baca yang disarankan: `inventori/10` → `inventori/20` → `produk/10` →
`produk/20` + `produk/21` → `produk/30` → `platform/*` → `notifikasi/10` → `metrik/10`.

## 3. Stack Terkunci Varian 1

- Mobile utama: Kotlin 2.2.20 + Compose Multiplatform 1.8.2 + Navigation3 1.0.0 +
  Ktor 3.1.3 + SQLDelight 2.1.0 (Android 9+, iOS 16+).
- Web pendamping: Bun 1.4.4 + Svelte 5.28.0 + SvelteKit 2.22.0 + TypeScript 5.9.2 +
  Elysia 1.3.8 + Tailwind 4.1.12.
- Bersama: modul KMP `:shared:pantry-resep:1.0.0` (`id.pedaree.pantryresep`).
- Server: `https://api.pedaree.id/v1`, job `expiry-sweep` 06:00 WIB.

## 4. Tautan Silang

- Pawonee rekomendasi resep (konsumen modul pantry-resep):
  https://github.com/Coding-Skuy/Pawonee-TownHall
- TitipO pesanan (penerima sinyal demand MENIPIS/HABIS):
  https://github.com/Coding-Skuy/TitipO-TownHall
- Kontrak yang menyentuh repo lain: `produk/30-modul-KMP-bersama.md` (Pawonee),
  `produk/20-kontrak-api-KMP-mobile.md` bab 2.5 + `produk/10-alur-catat-stok.md`
  Alur A/B (TitipO).

## 5. Status

Varian 1 disetujui. Perubahan memerlukan MR dengan label `varian-2`.
