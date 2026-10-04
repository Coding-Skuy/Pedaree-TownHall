# 30 — Modul KMP Bersama pantry-resep (Varian 1)

Status: disetujui Varian 1. Bahasa: Indonesia.

## 1. Tujuan

Satu modul Kotlin Multiplatform dipakai bersama Pedaree (stok dapur) dan Pawonee
(rekomendasi resep) agar definisi bahan, satuan, FEFO, dan skor resep tidak bercabang.
Pemilik modul: Pedaree. Pawonee hanya mengonsumsi API publik modul.

## 2. Koordinat Modul

- Nama Gradle: `:shared:pantry-resep`, grup `id.pedaree`, versi `1.0.0` (Varian 1 terkunci).
- Paket akar: `id.pedaree.pantryresep`.
- Target KMP: `androidTarget`, `iosX64`, `iosArm64`, `iosSimulatorArm64`, `jvm` (untuk tes).
- Dependensi: `kotlinx-serialization-json 1.8.1`, `kotlinx-datetime 0.6.2`, tanpa Ktor,
  tanpa Compose, tanpa SQLDelight (modul murni logika + model).

## 3. API Publik (tidak boleh diubah tanpa naik versi minor)

```kotlin
package id.pedaree.pantryresep

enum class Kategori { SAYUR, BUMBU, BUAH, PROTEIN, KERING, CAIR, BEKU, SISA }
enum class Satuan { gram, kg, ml, liter, sdm, sdt, pcs, siung, buah, ikat, batang, bonggol, lembar }
enum class LokasiSimpan { KULKAS_BAWAH, KULKAS_ATAS, FREEZER, SUHU_RUANG, BASKOM_BAWANG }
enum class StatusBatch { AKTIF, HABIS, KEDALUWARSA, DIBUANG }

fun konversiKeSimpan(jumlah: Double, dari: Satuan, komoditas: String): Double
fun tampilkan(jumlahSimpanGramAtauMl: Double, tampil: Satuan, komoditas: String): String
fun pilihBatchFefo(batches: List<StokBatch>): List<StokBatch>
fun hitungKecocokanResep(stok: Map<String, Double>, kebutuhan: Map<String, Double>): SkorResep
data class SkorResep(val persen: Int, val kurang: Map<String, Double>)
```

- `konversiKeSimpan` memakai tabel bobot `inventori/20-satuan-hortikultura.md` yang
  dikodekan sebagai `BobotAcuan.kt` (12 komoditas Varian 1).
- `pilihBatchFefo` mengurutkan batch AKTIF berdasarkan tanggalKedaluwarsa paling dekat.
- `hitungKecocokanResep` dipakai Pawonee untuk mengurutkan rekomendasi:
  `persen = 100 × Σ min(stok, butuh) ÷ Σ butuh`, dibulatkan ke bawah.

## 4. Aturan Berbagi dengan Pawonee

1. Pawonee bergantung pada versi `1.0.0` persis (`implementation("id.pedaree:pantry-resep:1.0.0")`).
   Kenaikan versi dilakukan bersama tiap rilis minor.
2. Perubahan tabel bobot, enum, atau rumus skor wajib lewat MR ke repo Pedaree-TownHall
   dengan label `pantry-resep` dan notifikasi ke pengelola Pawonee.
3. Modul tidak menyimpan rahasia, tidak memanggil jaringan, tidak membaca platform.
   Fungsi waktu menerima `hariIni: LocalDate` sebagai parameter agar bisa diuji.
4. Cakupan tes minimum 90% untuk `konversi`, `fefo`, dan `skor`; tes berada di
   `src/commonTest` dan jalan di CI Android + iOS.

## 5. Contoh Pakai Lintas Aplikasi

- Pedaree Catat Pakai: `pilihBatchFefo(batchesAktif)` menentukan urutan potong.
- Pawonee Rekomendasi: `hitungKecocokanResep(stokPedaree, kebutuhanResep)` menentukan
  urutan kartu resep; bahan yang SEGERA/HARI_INI diberi bonus +15 poin di sisi Pawonee.
- Sinyal `kurang` dari `SkorResep` dipakai Pedaree untuk tombol `Tambah ke TitipO`.
