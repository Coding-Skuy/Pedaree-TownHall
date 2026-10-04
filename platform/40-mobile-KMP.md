# 40 — Mobile KMP (Varian 1)

Status: disetujui Varian 1. Bahasa: Indonesia.

## 1. Sasaran

Aplikasi utama Pedaree di Android 9+ dan iOS 16+. Catat stok harian < 30 detik,
jalan luring penuh, push kedaluwarsa tepat waktu.

## 2. Versi Terkunci

| Komponen | Versi |
|---|---|
| Kotlin | 2.2.20 |
| Android Gradle Plugin | 8.9.2 |
| Compose Multiplatform | 1.8.2 |
| Navigation3 runtime+ui | 1.0.0 |
| Ktor client | 3.1.3 |
| kotlinx-serialization-json | 1.8.1 |
| kotlinx-datetime | 0.6.2 |
| Koin | 4.1.0 |
| SQLDelight | 2.1.0 |
| WorkManager (Android) | 2.10.0 |
| FCM | 24.1.0 |

## 3. Struktur Modul

```
:composeApp         → UI Compose Multiplatform + Navigation3 + ViewModel
:shared:pantry-resep → logika murni bersama Pawonee (lihat produk/30-modul-KMP-bersama.md)
:shared:sync        → antre luring, Ktor client, mapper DTO ↔ SQLDelight
:shared:data        → SQLDelight (tabel PantryItem, StokBatch, Mutasi, AntreTulis)
```

Dependensi hanya searah: `composeApp → sync → data`, `composeApp → pantry-resep`.
Modul `pantry-resep` tidak bergantung pada modul lain.

## 4. Pola Lapisan

- UI: satu `ViewModel` per rute Navigation3, state `StateFlow<UiState>`,
  event satu-kali via `Channel`.
- Repository `PantryRepository` adalah satu-satunya pintu ke data:
  baca dari SQLDelight dulu (luring), tulis ke antre lalu sinkron.
- Ktor client memakai `ContentNegotiation(json)`, timeout 15 detik,
  header `X-Request-Id` per mutasi untuk idempotensi server.
- DI Koin: `appModule` (platform), `syncModule` (client, repo), `pantryResepModule` (pure).

## 5. Penyimpanan Lokal

SQLDelight 2.1.0, file `pedaree.db`, 4 tabel mirror server + 1 tabel `AntreTulis`
(id, requestId, method, path, bodyJson, dibuatPada, percobaan).
Migrasi skema bernomor (`1.sqm`, `2.sqm`); Varian 1 dikirim dengan skema 2.

## 6. Kriteria Terima Varian 1

- Dingin-start < 2 detik di Pixel 6a dan iPhone 12.
- Catat pakai luring tersinkron < 60 detik setelah daring kembali.
- Push WASPADA/SEGERA/HARI_INI tiba sesuai jadwal `notifikasi/10-sop-kedaluwarsa.md`.
- Tes: `commonTest` modul bersama ≥ 90%, UI test 6 alur `produk/10-alur-catat-stok.md` lolos.
