> Versi: v1.0.0 | Status: disetujui | Menggantikan: -

# SNAPSHOT-ROADMAP v1.0.0 — Salinan Beku

Salinan beku janji v1.0.0 pada 10 Okt 2026. Tidak diubah lagi. Perubahan masa depan dicatat di `roadmap/` living dan dirilis sebagai versi baru.

## Janji Beku

- Skala akhir pilot: 100 rumah tangga aktif, 12 bahan awal, 13 satuan hortikultura baku, FEFO penuh.
- Expiry: ambang AMAN lebih dari 7 hari, WASPADA 4 sampai 7 hari, SEGERA 1 sampai 3 hari, HARI_INI 0 hari, KEDALUWARSA lewat tanggal. Job `expiry-sweep` 06:00 WIB, push 06:30 sampai 20:00 WIB.
- Sinyal: MENIPIS saat tampil kurang dari sama dengan minimum dan lebih dari 0, HABIS saat 0. Debounce 1 sinyal per item per 24 jam. Event `pedaree.stok.v1` terbit tiap mutasi pemicu dan diteruskan pipeline ke TitipO.
- Lulus pilot: stockout harian rata-rata di bawah 8 persen, waste di bawah 1.500 gram dan Rp50.000 per rumah tangga per bulan, 60 persen sinyal berujung pesan TitipO 48 jam, 50 persen batch SEGERA dipakai.
- Sistem: mobile KMP Android 9 plus dan iOS 16 plus offline-first, web Bun 1.4 dan Svelte 5 pendamping, backend tunggal Pedaree-Backend di atas DB `pedaree`, autentikasi JWT audien `pedaree`, modul `:shared:pantry-resep` 1.0.0 milik Pawonee dikonsumsi tanpa fork.

## Sumber

- Dibekukan dari `inventori/`, `notifikasi/`, `produk/`, `platform/`, `metrik/` versi lama ditambah target stockout dan waste. File lama sudah dipindah dengan `git mv` dan dihapus dari lokasi asal.

## Batasan

Batasan dokumen ini: hanya salinan janji saat v1.0.0 disetujui. Tidak menjadi acuan operasional terkini; acuan terkini ada di `roadmap/TIMELINE.md` dan `roadmap/MILESTONE.md`.
