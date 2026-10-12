> Versi: v1.0.0 | Status: disetujui | Menggantikan: -

# Platform ~ Aplikasi (Pedaree)

Folder ini adalah **indeks aplikasi** divisi Pedaree: tujuh repo kode yang hidup di
organisasi Coding-Skuy ([tim Pedaree](https://github.com/orgs/Coding-Skuy/teams/pedaree/repositories)),
ditautkan ke repo ini sebagai **git submodule**.

- `platform/versions/` — dokumen formal beku (BRD/PRD/FRD/FSD/SRS).
- `platform/apps/` — kode aplikasi (submodule, repo terpisah).

## Repo aplikasi

| Submodule | Repo | Peran | Stack |
|---|---|---|---|
| `app-kmp/` | [pedaree-app-kmp](https://github.com/Coding-Skuy/pedaree-app-kmp) | Aplikasi klien (FEFO, catat stok) | Kotlin MPP / CMP |
| `web/` | [pedaree-web](https://github.com/Coding-Skuy/pedaree-web) | Web pendamping | Bun + SvelteKit + TS |
| `backend-service/` | [pedaree-backend-service](https://github.com/Coding-Skuy/pedaree-backend-service) | Layanan API inventori dapur | Rust |
| `ai-models/` | [pedaree-ai-models](https://github.com/Coding-Skuy/pedaree-ai-models) | Model Pedaree | Python (training) + Rust (serving) |
| `data-pipeline/` | [pedaree-data-pipeline](https://github.com/Coding-Skuy/pedaree-data-pipeline) | Pipeline data Pedaree | — |
| `design/` | [pedaree-design](https://github.com/Coding-Skuy/pedaree-design) | Desain Pedaree | Figma |
| `infra-devops/` | [pedaree-infra-devops](https://github.com/Coding-Skuy/pedaree-infra-devops) | Infra & deploy Pedaree | CI/CD |

## Cara pakai

```bash
# clone beserta submodule
git clone --recurse-submodules https://github.com/Coding-Skuy/Pedaree-TownHall.git
# bila repo sudah ter-clone tanpa submodule:
git submodule update --init --recursive
# perbarui ke commit main terbaru tiap repo app:
git submodule update --remote
```

## Batasan

Indeks ini hanya menautkan repo. Kode dan riwayatnya tetap milik repo app masing-masing;
keputusan versi di tulis di `platform/versions/vX.Y.Z/`, bukan di sini.