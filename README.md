> Versi: v1.0.0 | Status: disetujui | Menggantikan: -

# Pedaree-TownHall — Divisi Smart Pantry (Stok Dapur) PT ChefGenie

## Peran Pedaree

Pedaree adalah divisi Smart Pantry PT ChefGenie: sumber kebenaran stok dapur rumah tangga. Mencatat apa yang ada, mengingatkan kapan kedaluwarsa, dan mengirim sinyal demand saat stok menipis atau habis. Masuk: belanja manual, panen, parcel TitipO yang diterima. Keluar: dipakai memasak termasuk via rekomendasi Pawonee, opname, buang. Sinyal keluar: demand MENIPIS dan HABIS ke TitipO lewat event `pedaree.stok.v1`; kebutuhan resep dibaca dari modul bersama sebagai KONSUMEN. Disiplin pengeluaran: FEFO, batch dengan tanggal kedaluwarsa terdekat dipakai lebih dulu, bila null maka FIFO batch tertua dulu. Satuan jumlah terbatas pada 13 satuan hortikultura baku: `kg`, `g`, `ons`, `buah`, `butir`, `siung`, `ikat`, `batang`, `lembar`, `bonggol`, `pak`, `ml`, `liter`. Basis data tunggal: DB `pedaree`. Autentikasi: JWT dengan audien `pedaree`. Modul `:shared:pantry-resep` adalah milik Pawonee; Pedaree hanya mengonsumsi sebagai KONSUMEN dan tidak mengubah definisi resep.

## Peta Versi Aktif

- Versi aktif: v1.0.0 (disetujui). Isi beku ada di `versions/v1.0.0/`.
- `versions/v1.0.0/CHANGELOG.md` — ringkasan versi awal.
- `versions/v1.0.0/BRD/` — kebutuhan bisnis BR-001 dan seterusnya.
- `versions/v1.0.0/PRD/` — pengguna dan kriteria US-001 dan seterusnya.
- `versions/v1.0.0/FRD/` — kebutuhan fungsional FR-001 dan seterusnya.
- `versions/v1.0.0/FSD/` — rancangan alur, model data PantryItem, StokBatch, MutasiStok, dan kontrak API.
- `versions/v1.0.0/SNAPSHOT-ROADMAP.md` — salinan beku janji v1.0.0.
- Peta hidup lintas versi ada di `roadmap/`: `TIMELINE.md`, `MILESTONE.md`, `ROADMAP.md`.

## Cara Baca History

1. Mulai dari `versions/v1.0.0/CHANGELOG.md` untuk ringkasan versi.
2. Lanjut ke `versions/v1.0.0/BRD/00-ikhtisar.md` untuk konteks bisnis, lalu `PRD/10-pengguna.md` untuk peran.
3. Untuk janji waktu itu, baca `versions/v1.0.0/SNAPSHOT-ROADMAP.md` yang sudah dibekukan dan tidak diubah lagi.
4. Untuk kondisi terkini lintas versi, baca `roadmap/TIMELINE.md` dan `roadmap/MILESTONE.md`.
5. Riwayat perubahan antar versi dilacak lewat `git log` dan `CHANGELOG.md` tiap versi. File lama sengaja dihapus setelah dipindah dengan `git mv` agar tidak ada dua sumber kebenaran.

## TownHall Lain dan Pedoman Induk

Pedoman induk: https://github.com/Coding-Skuy/ChefGenie-TownHall.

Lima TownHall lain yang meniru pola template emas ini:

- https://github.com/Coding-Skuy/Lumbung-TownHall — hulu agregasi dan distribusi.
- https://github.com/Coding-Skuy/Pawonee-TownHall — dapur dan pengolahan, pemilik modul `:shared:pantry-resep`.
- https://github.com/Coding-Skuy/Pasaree-TownHall — pasar dan penjualan.
- https://github.com/Coding-Skuy/TitipO-TownHall — titip dan kemitraan, penerima sinyal demand dari event `pedaree.stok.v1`.
- https://github.com/Coding-Skuy/Titeny-TownHall — ketelitian dan audit mutu.

Pola yang ditiru: penamaan `versions/vX.Y.Z/BRD|PRD|FRD|FSD/`, file `NN-nama-kebab.md`, header versi satu baris, dan bagian Batasan di tiap file.

## Batasan

Batasan ruang lingkup repo ini: hanya stok dapur, expiry FEFO, 13 satuan hortikultura, sinyal demand ke TitipO via event `pedaree.stok.v1`, dan kontrak API Pedaree di atas DB `pedaree` dengan JWT audien `pedaree`. Di luar batas: definisi resep dan skor resep milik Pawonee, harga ecer pasar milik Pasaree, agregasi tani milik Lumbung, skema titip milik TitipO, dan audit independen milik Titeny. Pedaree sebagai KONSUMEN modul pantry-resep tidak mengubah skema resep Pawonee.
