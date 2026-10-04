# 50 — Web Bun + Svelte (Varian 1)

Status: disetujui Varian 1. Bahasa: Indonesia.

Web pendamping untuk rekap, opname massal, impor/ekspor, dan dasbor metrik.
Bukan pengganti mobile untuk catat harian.

## 1. Versi Terkunci

| Komponen | Versi |
|---|---|
| Bun | 1.4.4 |
| Svelte | 5.28.0 |
| SvelteKit | 2.22.0 |
| TypeScript | 5.9.2 |
| Vite | 6.3.5 |
| Elysia (API) | 1.3.8 |
| adapter-node | 2.1.4 |
| Tailwind CSS | 4.1.12 |
| better-sqlite3 via bun:sqlite | bawaan Bun 1.4.4 |
| Vitest | 3.2.4 |
| Playwright | 1.55.0 |

Perintah baku: `bun install --frozen-lockfile`, `bun run dev` (port 5173),
`bun run build`, `bun run start` (port 3000), `bun run gen:schema`.

## 2. Struktur Folder

```
apps/web-pedaree/
  src/routes/
    dapur/+page.server.ts        → GET /pantry/items (SSR 60 detik)
    dapur/[id]/+page.server.ts   → GET /pantry/items/{id}
    opname/+page.svelte          → tabel opname massal
    dasbor/+page.server.ts        → GET /web/dashboard
    titipo/+page.svelte          → parcel + draf masuk
    masuk/+page.svelte           → impor CSV
  src/lib/
    api/client.ts                → fetch + cookie sesi + X-Request-Id
    api/types.ts                 → digenerate dari skema server
    stores/antre.ts              → antre luring (IndexedDB, Svelte 5 rune)
    komponen/KartuStok.svelte
    komponen/BannerKedaluwarsa.svelte
  server/
    index.ts                     → Elysia: /v1 + /web
    db/skema.sql                 → PantryItem, StokBatch, Mutasi, Sinyal
    jobs/expiry-sweep.ts         → cron 06:00 WIB
```

## 3. Aturan

1. Rune Svelte 5 (`$state`, `$derived`, `$effect`) untuk semua state baru;
   sintaks `$:` lama dilarang.
2. Semua tipe API di `src/lib/api/types.ts` digenerate (`bun run gen:schema`);
   edit manual dilarang.
3. SSR memakai `+page.server.ts` dengan cache 60 detik untuk baca,
   `no-store` untuk mutasi (lihat `produk/21-kontrak-api-web-bun.md`).
4. Antre luring web memakai IndexedDB `pedaree-antre` dengan skema sama
   seperti `AntreTulis` mobile; sinkron memakai protokol yang sama
   (`platform/60-offline-sinkron.md`).
5. Job `expiry-sweep` dan pengirim sinyal TitipO hanya jalan di server Bun,
   tidak di edge, tidak di cron klien.

## 4. Kriteria Terima Varian 1

- Lighthouse performa ≥ 90 di halaman `/dapur`.
- Impor 200 baris CSV selesai < 10 detik dengan laporan baris galat.
- `bun run build` bersih tanpa peringatan TypeScript.
- Tes Playwright 4 alur (masuk, pakai, opname, buang) lolos di Chromium.
