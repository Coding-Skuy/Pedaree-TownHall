# 10 — SOP Kedaluwarsa (Varian 1)

Status: disetujui Varian 1. Bahasa: Indonesia.

## 1. Tujuan

Memastikan tidak ada bahan kedaluwarsa yang dipakai tanpa peringatan,
mengurangi buangan (waste), dan mengubah bahan yang hampir kedaluwarsa
menjadi aksi: dimasak (sinyal ke Pawonee) atau dibuang dengan catat.

## 2. Ambang Waktu

Dihitung dari selisih hari kalender `hariIni − tanggalKedaluwarsa` per batch.
Batch dengan `tanggalKedaluwarsa = null` (garam, gula) tidak masuk SOP ini.

| Status | Syarat | Label UI |
|---|---|---|
| AMAN | > 7 hari tersisa | hijau |
| WASPADA | 4–7 hari tersisa | kuning, label `H-7` sampai `H-4` |
| SEGERA | 1–3 hari tersisa | oranye, label `H-3` sampai `H-1` |
| HARI_INI | 0 hari tersisa | merah, label `Pakai hari ini` |
| KEDALUWARSA | < 0 (lewat tanggal) | abu-abu merah, label `Kedaluwarsa` |

## 3. Notifikasi

### 3.1 Jadwal

- Job server `expiry-sweep` jalan tiap hari pukul 06:00 WIB.
- Evaluasi per batch, agregasi per item: tampilkan batch terdekat saja di daftar utama.
- Push mobile dikirim untuk transisi naik tingkat saja (AMAN→WASPADA→SEGERA→HARI_INI→KEDALUWARSA),
  maksimal 1 push per item per hari.

### 3.2 Isi Pesan (template baku)

- WASPADA: `[{nama}] sisa {sisa} — kedaluwarsa {tanggal}. Cocok dimasak minggu ini.`
- SEGERA: `[{nama}] sisa {sisa} — tinggal {n} hari. Lihat resep Pawonee atau catat pakai.`
- HARI_INI: `[{nama}] sisa {sisa} — pakai hari ini atau buang dan catat.`
- KEDALUWARSA: `[{nama}] {sisa} kedaluwarsa {tanggal}. Buang dan catat agar metrik waste akurat.`

Jam pengiriman push: 06:30–20:00 WIB. Di luar jam itu pesan antre sampai 06:30.

### 3.3 Kanal Varian 1

- Mobile KMP: push (FCM) + badge angka di tab Dapur + banner di detail item.
- Web Bun: badge + banner, tanpa push browser Varian 1.
- Tidak ada SMS, email, maupun WhatsApp Varian 1.

## 4. Aksi Wajib per Status

| Status | Aksi di UI | Tindak lanjut sistem |
|---|---|---|
| WASPADA | tombol `Lihat resep` | membuka rekomendasi Pawonee dengan bahan ini |
| SEGERA | tombol `Lihat resep` + `Catat pakai` | resep Pawonee diprioritaskan memakai bahan ini |
| HARI_INI | tombol `Catat pakai` + `Buang` | `Catat pakai` mengurangi FEFO, `Buang` menulis mutasi BUANG |
| KEDALUWARSA | hanya tombol `Buang dan catat` | item tidak bisa dipilih di `Catat pakai`; batch terkunci dari FEFO |

## 5. Aturan Buang

1. Buang wajib memilih alasan: `busuk`, `berlendir/berbau`, `lewat tanggal`, `tercemar`.
2. Buang menulis mutasi BUANG dan mengubah status batch menjadi DIBUANG.
3. Buang sebagian diizinkan: jumlah buang ≤ jumlahAktif, sisa tetap AKTIF bila belum lewat tanggal.
4. Batch KEDALUWARSA yang dibuang masuk metrik waste bulan berjalan.

## 6. Pengecualian

- Sisa masakan (kategori SISA) memakai umur default 2 hari dan langsung berstatus SEGERA sejak H+1.
- Bahan beku (FREEZER) memakai tanggal yang sama tetapi label UI menambah keterangan `beku`;
  ambang tidak berubah.
