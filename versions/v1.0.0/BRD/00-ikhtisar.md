> Versi: v1.0.0 | Status: disetujui | Menggantikan: -

# BRD 00 — Ikhtisar Pedaree

## Konteks

Divisi Pedaree adalah Smart Pantry PT ChefGenie. Tugasnya menjadi sumber kebenaran stok dapur rumah tangga: mencatat apa yang ada, mengingatkan kapan kedaluwarsa, dan mengirim sinyal demand ke TitipO. Pedaree adalah KONSUMEN modul `:shared:pantry-resep`; pemilik modul adalah Pawonee. Basis data tunggal: DB `pedaree`. Sumber isi lama: `inventori/10-model-stok-dapur.md` bab peran dan `README.md` Varian 1.

## Kebutuhan Bisnis

- BR-001 Pedaree wajib mencatat stok 100 rumah tangga pilot dengan maksimum 400 PantryItem aktif dan 2.000 batch per rumah tangga pada akhir pilot 90 hari.
- BR-002 Pengeluaran stok wajib FEFO: batch dengan tanggal kedaluwarsa paling dekat keluar lebih dulu; bila null maka FIFO batch tertua dulu. Batch KEDALUWARSA terkunci dari FEFO.
- BR-003 Satuan jumlah wajib termasuk 13 satuan hortikultura baku: `kg`, `g`, `ons`, `buah`, `butir`, `siung`, `ikat`, `batang`, `lembar`, `bonggol`, `pak`, `ml`, `liter`. Satuan di luar daftar ditolak pada validasi domain.
- BR-004 Setiap mutasi pemicu wajib menerbitkan event `pedaree.stok.v1` dengan jenis `stok.menipis`, `stok.habis`, atau `kedaluwarsa.dekat`; sinyal MENIPIS dan HABIS diteruskan pipeline ke TitipO dengan debounce 1 sinyal per item per 24 jam.
- BR-005 Sukses diukur sebagai ketersediaan dan hemat buang: stockout harian rata-rata di bawah 8 persen, waste di bawah 1.500 gram dan Rp50.000 per rumah tangga per bulan, 60 persen sinyal MENIPIS berujung pesan TitipO 48 jam, 50 persen batch SEGERA dipakai bukan dibuang.
- BR-006 Operasional expiry: job `expiry-sweep` 06:00 WIB tiap hari, push 06:30 sampai 20:00 WIB, ambang WASPADA H-7 sampai H-4, SEGERA H-3 sampai H-1, HARI_INI 0 hari, KEDALUWARSA lewat tanggal.
- BR-007 Autentikasi lixo: JWT dengan audien `pedaree`, header `X-Household-Id` dan `X-Request-Id`, tanpa akun bersama. Satu rumah tangga satu token 30 hari mobile dan cookie sesi web.

## Metrik

- Stok tampil per item, sisa hari kedaluwarsa, stockoutRate, wasteGram dan wasteRupiah, waktu menipis ke habis median, persen sinyal berujung pesan TitipO. Antrean tulis tidak pernah melebihi 500 baris per perangkat.

## Batasan

Batasan dokumen ini: hanya menyatakan kebutuhan bisnis dan angka ambang. Cara pemenuhan diatur di PRD, FRD, dan FSD. Di luar batas: penentuan resep milik Pawonee, penjualan ecer milik Pasaree, dan penawaran akhir TitipO yang hanya menerima sinyal anjuran.
