> Versi: v1.0.0 | Status: disetujui | Menggantikan: -

# FSD 30 — Kontrak API, Event, dan Galat

## Basis dan Autentikasi

- Basis: `https://api.pedaree.id/v1` dengan header `X-Household-Id` dan `X-Request-Id` UUID dan `X-Client`. Sehat: `GET /v1/kesehatan` kembali status ok dan waktu server.
- Autentikasi dikunci: JWT dengan audien `pedaree`, mobile Bearer 30 hari, web cookie `pedaree_sess` httpOnly. Rate-limit mobile 60 per menit per token, web 120 per menit per sesi. Kunci layanan server-ke-server dirotasi 90 hari dan tidak pernah ke browser.
- DB `pedaree` Postgres otoritatif; FEFO dihitung server; klien dilarang menghitung fee dan grade dan papan; semua memakai angka server.

## Kontrak per Konsumen

- Mobile KMP 7 endpoint: `GET /pantry/items`, `GET /pantry/items/{id}`, `POST /pantry/items`, `POST /pantry/batches` MASUK, `POST /pantry/consume` FEFO `pemakaian` plus `sumberResepId`, `POST /pantry/opname` dan `POST /pantry/batches/{id}/pindah` dan `POST /pantry/batches/{id}/buang`, `GET /pantry/expiring`, `GET /demand/signals`, `POST /demand/titipo` 202. Contoh consume membawa pantryItemId jumlah satuan dan kembali terpotong plus kurang.
- Web Bun 4 khusus: `GET /web/dashboard` totalItemAktif dan menipis dan habis dan segera3Hari dan wasteBulanGram dan sinyal7Hari via `+page.server.ts`, `POST /web/import/csv` 200 baris 512 KB, `GET /web/export/csv` dan `GET /web/export/mutasi`, `GET /web/titipo/parcels` dan `POST /web/titipo/parcels/{id}/draft-masuk`. SSR baca 60 detik, mutasi `no-store`.
- Modul bersama: `:shared:pantry-resep` 1.0.0 grup `id.pedaree` paket `id.pedaree.pantryresep` milik Pawonee; Pedaree KONSUMEN via `implementation id.pedaree:pantry-resep:1.0.0`. API `konversiKeSimpan`, `tampilkan`, `pilihBatchFefo`, `hitungKecocokanResep` persen 100 kali min stok butuh bagi butuh. Tanpa Ktor dan Compose dan SQLDelight; tes 90 persen konversi dan fefo dan skor.

## Event dan Galat

- Event: `stok.menipis`, `stok.habis`, `kedaluwarsa.dekat` pada topik `pedaree.stok.v1` berisi event dan item dan sisa dan satuan 13 baku dan kedaluwarsa dan `recipe_id` Pawonee. Setiap mutasi pemicu menerbitkan 1 event; pipeline meneruskan anjuran ke TitipO webhook. Analitik `stok_menipis` dan `stok_habis` dan `buang_catat` dan `sinyal_titipo` dan `pakai_dari_resep`.
- Idempotensi: semua POST membawa `X-Request-Id`; kirim ulang sama kembali 200 tanpa ganda 24 jam.
- Galat baku: 200 OK, 201 Created, 202 Diterima, 400 validasi satuan tidak berlaku dan jumlah di atas 0, 401 token salah masuk ulang, 404 tidak ada, 409 `KONFLIK_OPNAME` serverStok, 429 rate-limit. Body `{ kode, pesan, field }`.

## Batasan

Batasan dokumen ini: hanya kontrak, event, dan galat. Implementasi server ada di repo Pedaree-Backend. Konsumen dilarang menerbitkan `pedaree.stok.v1` dari klien; hanya backend. Perubahan `recipe_id` dikoordinasikan lewat TownHall Pawonee.
