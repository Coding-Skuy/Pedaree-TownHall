# 60 — Offline & Sinkron (Varian 1)

Status: disetujui Varian 1. Bahasa: Indonesia.

## 1. Prinsip

- Baca selalu dari lokal dulu (SQLDelight di mobile, IndexedDB + cache SSR di web).
- Tulis selalu masuk antre lokal dulu, UI optimistis, sinkron di latar.
- Server adalah penengah akhir untuk urutan mutasi; klien tidak pernah
  menulis langsung ke DB server.

## 2. Bentuk Antrean

Setiap baris antre: `{ requestId UUID, method, path, bodyJson, dibuatPada, percobaan }`.
`requestId` dikirim sebagai `X-Request-Id`; server menyimpan hasil 24 jam
sehingga kirim ulang aman (idempoten).

Batas Varian 1: 500 baris antre per perangkat, ukuran body ≤ 64 KB per baris,
umur baris maksimal 14 hari (lebih lama ditandai gagal permanen).

## 3. Urutan dan Konflik

1. Antre dikirim FIFO per perangkat.
2. Backoff: 5 detik, 30 detik, 5 menit, 30 menit, lalu 6 jam. Setelah 20 gagal,
   baris ditandai `GAGAL` dan pengguna diminta meninjau.
3. Konflik opname: bila server mencatat mutasi lebih baru untuk item yang sama
   setelah `dibuatPada` baris antre, server menolak dengan 409
   `{ "kode": "KONFLIK_OPNAME", "serverStok": 650 }`. Klien menampilkan dialog
   `Server 650 g vs kamu 500 g` dengan pilihan `Pakai server` atau `Timpa dengan punyaku`
   (timpa menulis KOREKSI baru, bukan menimpa sejarah).
4. FEFO dihitung ulang di server saat sinkron; urutan potong pratinjau klien
   bisa berbeda dengan hasil server bila ada tulis sejajar — hasil server menang.

## 4. Sinyal Demand Tetap Terkirim

- Sinyal MENIPIS/HABIS dihitung di server dari hasil akhir sinkron,
  bukan dari pratinjau klien.
- Debounce 24 jam per item tetap berlaku walau ada 10 tulis luring berurutan.
- Penerusan ke TitipO (`POST /demand/titipo`) ikut antre bila luring;
  status `ANTRE` terlihat di `GET /demand/signals`.

## 5. Indikator UI

- Lencana `Luring — {n} antre` di header mobile dan web.
- Baris antre yang GAGAL menampilkan tombol `Coba lagi` dan `Hapus`.
- Tidak ada tombol `Sinkron sekarang` manual Varian 1; sinkron dipicu oleh
  kembali daring, buka aplikasi, dan worker 15 menit (WorkManager Android,
  BGTask iOS, interval 5 menit web saat tab terbuka).
