# 20 — Kontrak API KMP Mobile (Varian 1)

Status: disetujui Varian 1. Bahasa: Indonesia.

Klien: aplikasi KMP + Compose Multiplatform + Navigation3 (lihat `platform/40-mobile-KMP.md`).
Server: Bun 1.4.4 + Elysia 1.3.8 (lihat `platform/50-web-bun-svelte.md`).
Format: JSON, UTF-8, waktu RFC3339 UTC, tanggal YYYY-MM-DD.

## 1. Fondasi

- Basis: `https://api.pedaree.id/v1`, versioning lewat path (`/v1` tetap Varian 1).
- Auth: Bearer JWT rumah tangga (`Authorization: Bearer <token>`). Token kedaluwarsa 30 hari.
- Header wajib: `X-Household-Id`, `X-Client: kmp-mobile/1.0.0`, `X-Request-Id` (UUID per request).
- Kode status: 200 OK, 201 Created, 400 validasi, 401 token salah, 404 tidak ada, 409 konflik opname, 429 rate-limit.
- Body galat baku: `{ "kode": "STOK_KURANG", "pesan": "Stok tomat kurang 200 g", "field": "jumlah" }`.
- Pagination: `?limit=20&cursor=<opaque>`; respons `{ "data": [], "nextCursor": null }`.

## 2. Endpoint

### 2.1 Daftar dan Detail Stok

- `GET /pantry/items?q=&kategori=&statusStok=&limit=&cursor=`
  → 200 `{ "data": [PantryItemRingkas], "nextCursor": "..." }`.
  `PantryItemRingkas`: id, nama, kategori, stokTampil, satuanTampil, statusStok
  (AMAN, MENIPIS, HABIS), batchTerdekatKedaluwarsa, lokasiDefault.
- `GET /pantry/items/{id}` → 200 PantryItem penuh + `batches: StokBatch[]` + `stokTampil`.
- `POST /pantry/items` → 201 PantryItem. Body: nama, kategori, satuanSimpan,
  satuanTampil, stokMinimum, umurSimpanHariDefault, lokasiDefault.

### 2.2 Catat Masuk

- `POST /pantry/batches` → 201 StokBatch + mutasi MASUK.
  Body: `{ "pantryItemId": "...", "jumlah": 1000, "satuan": "gram", "tanggalMasuk": "2026-09-28", "tanggalKedaluwarsa": "2026-10-19", "lokasi": "BASKOM_BAWANG", "sumber": "MASUK_BELANJA" }`.

### 2.3 Catat Pakai (FEFO di server)

- `POST /pantry/consume` → 200 `{ "terpotong": [{batchId, jumlah}], "kurang": [] }`.
  Body: `{ "pemakaian": [{ "pantryItemId": "...", "jumlah": 200, "satuan": "gram" }], "sumberResepId": "pwn_r1234 atau null" }`.
  Bila stok kurang: 200 dengan `kurang: [{pantryItemId, kurangJumlah, satuanSimpan}]`
  plus tombol klien `Tambah ke TitipO`.

### 2.4 Opname, Pindah, Buang

- `POST /pantry/opname` → 200 `{ "mutasi": [...] }`.
  Body: `{ "pantryItemId": "...", "stokFisik": 650, "satuan": "gram", "alasan": "susut bayam" }`.
- `POST /pantry/batches/{id}/pindah` → 200 batch baru-lokasi.
  Body: `{ "lokasiTujuan": "KULKAS_BAWAH" }`.
- `POST /pantry/batches/{id}/buang` → 200 mutasi BUANG.
  Body: `{ "jumlah": 150, "satuan": "gram", "alasan": "lewat tanggal" }`.

### 2.5 Kedaluwarsa dan Sinyal Demand

- `GET /pantry/expiring?dalamHari=7` → 200 `{ "data": [{batchId, pantryItemId, nama, sisa, satuanTampil, tanggalKedaluwarsa, status}] }` terurut FEFO.
- `GET /demand/signals?sejak=` → 200 daftar sinyal yang pernah dipicu
  `{ "itemId": "...", "jenis": "MENIPIS|HABIS", "dipicuPada": "...", "statusKirimTitipO": "TERKIRIM|ANTRE" }`.
- `POST /demand/titipo` → 202 meneruskan keranjang ke TitipO.
  Body: `{ "items": [{ "pantryItemId": "...", "jumlah": 500, "satuan": "gram" }] }`.

## 3. Model Kotlin (shared)

```kotlin
@Serializable
data class PantryItem(
  val id: String, val householdId: String, val nama: String,
  val kategori: Kategori, val satuanSimpan: Satuan, val satuanTampil: Satuan,
  val stokMinimum: Double, val umurSimpanHariDefault: Int,
  val lokasiDefault: LokasiSimpan, val fotoUrl: String? = null,
  val arsip: Boolean = false
)
@Serializable
data class StokBatch(
  val id: String, val pantryItemId: String,
  val jumlahAwal: Double, val jumlahAktif: Double,
  val tanggalMasuk: String, val tanggalKedaluwarsa: String?,
  val lokasi: LokasiSimpan, val sumber: SumberBatch, val status: StatusBatch
)
```

Enum `Kategori`, `LokasiSimpan`, `SumberBatch`, `StatusBatch` sama persis dengan
`inventori/10-model-stok-dapur.md` dan hidup di modul bersama
(`produk/30-modul-KMP-bersama.md`).

## 4. Sinkron Luring

Klien menandai setiap POST dengan `X-Request-Id`. Server menyimpan hasil 24 jam
untuk idempotensi: kirim ulang dengan ID sama mengembalikan hasil asli tanpa
mutasi ganda. Detail antrean di `platform/60-offline-sinkron.md`.
