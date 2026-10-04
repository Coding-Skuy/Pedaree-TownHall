> Versi: v1.0.0 | Status: disetujui | Menggantikan: -

# FSD 10 — Alur Sistem

## Urutan Tulis Lokal Dahulu

1. Perangkat menulis ke basis lokal dahulu dalam kurang dari 200 ms dengan ULID dan UUID request, lalu menampilkan sukses tanpa menunggu server.
2. Antrean outbox FIFO diproses saat online dengan backoff 5 detik, 30 detik, 5 menit, 30 menit, lalu 6 jam. Setelah 20 gagal baris GAGAL dan minta tinjau. Batas 500 baris, body 64 KB, umur 14 hari.
3. `X-Request-Id` UUID per tulis disimpan server 24 jam untuk idempotensi: kirim ulang sama kembali 200 tanpa mutasi ganda. Mobile antre SQLDelight `AntreTulis`, web antre IndexedDB `pedaree-antre` skema sama.
4. Resolusi konflik: server menang untuk urutan FEFO akhir; opname sejajar ditolak 409 `KONFLIK_OPNAME` dengan `serverStok`; klien pilih pakai server atau timpa sebagai KOREKSI baru. Setiap konflik dicatat dan diberi tahu.
5. Batas darurat: tidak ada tombol sinkron manual; sinkron dipicu kembali daring, buka aplikasi, worker 15 menit mobile dan 5 menit web saat tab terbuka. Lencana luring plus n antre di header.

## Urutan Layar Mobile dan Web

- Mobile KMP: Dapur DaftarStok, DetailItem, CatatPakai dan CatatMasuk dan Opname dan PindahBatch dan BuangBatch sebagai dialog-scene, TambahBahan, ResepPawonee bahan fokus, KonfirmasiMasak kembali DetailItem snackbar urungkan 10 detik sebagai KOREKSI pembalik, TitipODraf, Pengaturan. Satu tumpukan per tab bawah.
- Web Bun: `/dapur` SSR 60 detik, `/dapur/[id]` detail, `/opname` tabel massal, `/dasbor` agregasi, `/titipo` parcel plus draf, `/masuk` impor CSV 200 baris 512 KB. Mutasi `no-store`. Rune Svelte 5 `$state` dan `$derived` dan `$effect`; tipe digenerate `bun run gen:schema`.
- Server: tulis FEFO dihitung ulang saat sinkron; pratinjau klien bisa beda bila tulis sejajar dan hasil server menang. Sinyal MENIPIS dan HABIS dihitung dari hasil akhir sinkron dengan debounce 24 jam.

## Batasan

Batasan dokumen ini: hanya urutan sistem dan aturan sinkron. Formula bisnis ada di FSD model data dan kontrak. Di luar batas: desain visual dan merek. Target mutu: dingin-start kurang dari 2 detik, sinkron kurang dari 60 detik, impor 200 baris kurang dari 10 detik, Lighthouse `/dapur` minimum 90.
