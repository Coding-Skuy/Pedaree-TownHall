> Versi: v1.0.0 | Status: disetujui | Menggantikan: -

# PRD 30 — Kriteria Keberhasilan

## Cerita Pengguna dan Acceptance

- US-001 Sebagai penghuni dapur saya mencatat masuk sehingga stok bertambah akurat. Acceptance: jumlah di atas 0, tanggal masuk tidak masa depan, kedaluwarsa sama dengan atau setelah masuk kecuali null umur 0; 1 batch plus 1 mutasi MASUK tercatat; konversi 13 satuan memakai bobot acuan.
- US-002 Sebagai penghuni dapur saya mencatat pakai FEFO sehingga batch terdekat habis dulu. Acceptance: potong urut tanggalKedaluwarsa terdekat; batch KEDALUWARSA ditolak dengan pesan stok habis bila 0; kurang tampil jumlah kurang plus tombol TitipO.
- US-003 Sebagai admin rumah tangga saya opname sehingga catat sama dengan fisik. Acceptance: selisih otomatis KOREKSI alasan wajib; konflik opname 409 tampil server 650 gram vs kamu 500 gram dengan pilihan pakai server atau timpa dengan punyaku sebagai KOREKSI baru.
- US-004 Sebagai penghuni dapur saya menerima peringatan expiry sehingga tidak memakai kedaluwarsa. Acceptance: push transisi naik saja maksimal 1 per item per hari 06:30 sampai 20:00 WIB; KEDALUWARSA terkunci dari FEFO dan hanya tombol buang dan catat.
- US-005 Sebagai admin rumah tangga saya melihat dasbor sehingga hemat terukur. Acceptance: stockout harian di bawah 8 persen, waste di bawah 1.500 gram dan Rp50.000, mobile 3 kartu ringkas, web grafik 90 hari plus tabel plus 10 teratas.
- US-006 Sebagai TitipO saya menerima sinyal sehingga bisa menawarkan titip. Acceptance: MENIPIS dan HABIS terbit `pedaree.stok.v1` maksimal 1 per item per 24 jam; status TERKIRIM dan ANTRE terlihat di `GET /demand/signals`; 60 persen sinyal berujung pesan 48 jam.

## Non-Goals v1.0.0

- Tanpa SMS, email, WhatsApp; tanpa push browser web; tanpa tombol sinkron manual; tanpa penyimpanan skema resep permanen di Pedaree; tanpa pengiriman sinyal langsung dari klien ke TitipO.

## Batasan

Batasan dokumen ini: hanya kriteria produk yang dapat diuji. Rincian teknis API dan skema ada di FSD. Klaim sukses tanpa skor mingguan stockout, waste, sinyal, dan SEGERA dipakai dinyatakan tidak berlaku.
