# 20 — Navigasi3 (Varian 1)

Status: disetujui Varian 1. Bahasa: Indonesia.

Pustaka: `androidx.navigation3:navigation3-ui:1.0.0` + `navigation3-runtime:1.0.0`
di atas Compose Multiplatform (lihat `platform/40-mobile-KMP.md`).

## 1. Graf Rute

```
Dapur (DaftarStok)
├── DetailItem(itemId)
│   ├── CatatPakai(itemId, resepId?)
│   ├── CatatMasuk(itemId?)
│   ├── Opname(itemId)
│   ├── PindahBatch(batchId)
│   └── BuangBatch(batchId)
├── TambahBahan
├── ResepPawonee(bahanFokus?)
│   └── KonfirmasiMasak(resepId)
├── TitipO(DrafKeranjang)
└── Pengaturan
    └── ProfilRumahTangga
```

Deep link Varian 1:

- `pedaree://dapur/{itemId}` → DetailItem
- `pedaree://resep?bahan={nama}` → ResepPawonee terfilter
- `pedaree://titipo` → TitipO

## 2. NavKey (terkunci)

```kotlin
@Serializable
sealed interface PedareeNavKey {
  @Serializable data object DaftarStok : PedareeNavKey
  @Serializable data class DetailItem(val itemId: String) : PedareeNavKey
  @Serializable data class CatatPakai(val itemId: String, val resepId: String? = null) : PedareeNavKey
  @Serializable data class CatatMasuk(val itemId: String? = null) : PedareeNavKey
  @Serializable data class Opname(val itemId: String) : PedareeNavKey
  @Serializable data class PindahBatch(val batchId: String) : PedareeNavKey
  @Serializable data class BuangBatch(val batchId: String) : PedareeNavKey
  @Serializable data object TambahBahan : PedareeNavKey
  @Serializable data class ResepPawonee(val bahanFokus: String? = null) : PedareeNavKey
  @Serializable data class KonfirmasiMasak(val resepId: String) : PedareeNavKey
  @Serializable data object TitipODraf : PedareeNavKey
  @Serializable data object Pengaturan : PedareeNavKey
}
```

## 3. Aturan

1. Satu tumpukan navigasi per tab bawah (Dapur, Resep, TitipO, Pengaturan).
   Pindah tab menyimpan tumpukan masing-masing.
2. `CatatPakai`, `CatatMasuk`, `Opname`, `PindahBatch`, `BuangBatch` selalu dibuka
   sebagai dialog-scene (bukan full-screen) agar konteks item tidak hilang.
3. Hasil `KonfirmasiMasak` kembali ke `DetailItem` dengan snackbar
   `Stok berkurang {ringkasan}` dan tombol `Urungkan` selama 10 detik
   (urungkan = mutasi KOREKSI pembalik, bukan hapus baris).
4. State back-stack bertahan saat rotasi dan proses-dimati lewat `rememberNavBackStack`
   + `SavedStateHandle`; tidak ada rute dengan argumen nullable selain yang tertulis.
5. Setiap NavKey tercatat di analitik dengan nama kelasnya untuk metrik
   stockout/waste per layar.
