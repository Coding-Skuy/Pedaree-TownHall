# 21 — Kontrak API Web Bun (Varian 1)

Status: disetujui Varian 1. Bahasa: Indonesia.

Klien: web pendamping Bun 1.4.4 + Svelte 5.28 + SvelteKit 2.22 + TS 5.9.2
(lihat `platform/50-web-bun-svelte.md`). Server sama dengan kontrak mobile
(`https://api.pedaree.id/v1`), ditambah endpoint web khusus di bawah.
Versi dan format identik dengan `produk/20-kontrak-api-KMP-mobile.md`.

## 1. Perbedaan Web vs Mobile

- Web memakai cookie sesi `httpOnly` (`pedaree_sess`) sebagai pengganti header Bearer.
  Header `X-Household-Id` dan `X-Request-Id` tetap wajib.
- Web memakai SSR untuk daftar dapur dan halaman detail (SEO internal + cepat),
  mutasi tetap lewat `fetch` POST client-side agar antre luring jalan.
- Rate-limit web: 120 req/menit per sesi; mobile 60 req/menit per token.

## 2. Endpoint Khusus Web

### 2.1 Ringkasan Dasbor (SSR)

- `GET /web/dashboard` → 200:
```json
{
  "totalItemAktif": 12,
  "menipis": 2,
  "habis": 1,
  "segeraKedaluwarsa3Hari": 3,
  "wasteBulanGram": 420,
  "sinyalTerkirim7Hari": 5
}
```
  Diakses lewat SvelteKit `+page.server.ts` dengan `fetch` server-to-server memakai
  token layanan, bukan token pengguna.

### 2.2 Impor dan Ekspor

- `POST /web/import/csv` (multipart, maksimal 200 baris, 512 KB) → 202
  `{ "diterima": 200, "ditolak": 3, "galatBaris": [{ "baris": 17, "pesan": "satuan tidak berlaku" }] }`.
  Kolom CSV: `nama,kategori,jumlah,satuan,tanggalMasuk,tanggalKedaluwarsa,lokasi`.
- `GET /web/export/csv?kategori=` → 200 file `pedaree-stok-YYYYMMDD.csv` berisi stok tampil per item.
- `GET /web/export/mutasi?dari=&sampai=` → 200 CSV mutasi untuk audit rumah tangga.

### 2.3 Parcel TitipO

- `GET /web/titipo/parcels?status=DITERIMA` → daftar parcel yang bisa dijadikan
  draf Catat Masuk satu klik (lihat `produk/10-alur-catat-stok.md` Alur A).
- `POST /web/titipo/parcels/{id}/draft-masuk` → 201 daftar draf batch
  `{ "draf": [{ "pantryItemId": "...", "jumlah": 1000, "satuan": "gram" }] }`.

## 3. Tipe TypeScript (web)

```ts
export type StatusStok = 'AMAN' | 'MENIPIS' | 'HABIS';
export interface PantryItemRingkas {
  id: string; nama: string; kategori: string;
  stokTampil: number; satuanTampil: string;
  statusStok: StatusStok; batchTerdekatKedaluwarsa: string | null;
}
export interface DasborWeb {
  totalItemAktif: number; menipis: number; habis: number;
  segeraKedaluwarsa3Hari: number; wasteBulanGram: number;
  sinyalTerkirim7Hari: number;
}
```

Tipe ini digenerate dari skema JSON server (`bun run gen:schema`) sehingga nama field
tidak pernah menyimpang dari kontrak mobile.

## 4. Aturan SSR dan Cache

- Halaman `/dapur` dan `/dapur/[id]` diprarender ulang tiap 60 detik (`revalidate = 60`).
- Mutasi (masuk/pakai/opname/buang) tidak memakai cache: `cache: 'no-store'`.
- Galat 401 di SSR mengarahkan ke `/masuk`; galat 401 di fetch client menampilkan dialog masuk ulang.
