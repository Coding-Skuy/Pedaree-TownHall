# 10 — Matriks KMP vs Web (Varian 1)

Status: disetujui Varian 1. Bahasa: Indonesia.

Mobile KMP adalah aplikasi utama (catat harian, luring, push).
Web Bun adalah pendamping (rekap, impor/ekspor, opname besar).
Logika hitung (konversi, FEFO, skor) hanya ada di modul bersama
(`produk/30-modul-KMP-bersama.md`); tidak diduplikasi di JS/TS.

| Kemampuan | Mobile KMP | Web Bun | Pemilik logika |
|---|---|---|---|
| Catat masuk cepat | ya, 3 layar + kamera | ya, form + CSV | modul bersama + server |
| Catat pakai FEFO | ya | ya | server (FEFO), modul bersama (urutan tampil) |
| Opname / koreksi | ya, per item | ya, tabel massal + CSV | server |
| Pindah lokasi | ya | ya | server |
| Buang kedaluwarsa | ya | ya | server |
| Push kedaluwarsa | ya (FCM) | tidak (badge/banner saja) | server job `expiry-sweep` |
| Rekomendasi resep Pawonee | ya, kartu + `Masak ini` | ya, tautan baca saja | Pawonee, skor via modul bersama |
| Kirim sinyal ke TitipO | ya, tombol + otomatis | ya, tombol massal | server `/demand/titipo` |
| Impor/ekspor CSV | tidak | ya | server `/web/import`, `/web/export` |
| Dasbor stockout/waste | ringkas (3 kartu) | penuh (grafik + tabel) | server agregasi, lihat `metrik/10-stockout-dan-waste.md` |
| Luring penuh | ya, antre tulis | ya, antre tulis ringan | klien masing-masing, protokol di `platform/60-offline-sinkron.md` |
| Navigasi | Navigation3 (lihat `platform/20-navigasi3.md`) | SvelteKit router | masing-masing |

Aturan konflik: bila ada perbedaan perilaku, mobile KMP menjadi acuan,
web menyesuaikan pada rilis berikutnya.
