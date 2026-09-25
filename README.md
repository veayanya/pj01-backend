# InstaSave (Backend) — Proyek Magang Perangkat Lunak Web

Backend Python service **InstaSave** (`parth-dl`) yang dibuat oleh **Eva** untuk proyek magang perangkat lunak web. Service ini menyediakan CLI dan HTTP API lokal (`parth-dl serve`) untuk memproses dan mengunduh media publik Instagram (Reels, Posts, Carousels, dan Foto Profil).

- **Backend Tech Stack**: Python 3, HTTP API Server, CLI Tool
- **Deployment**: Render ([Repository Backend](https://github.com/veayanya/pj01-backend))
- **Dokumentasi**: [Backend Docs](https://github.com/veayanya/pj01-backend/tree/main/docs)
- **Developer**: Eva

## 🚀 Jalankan Lokal

```bash
pip install -e .
parth-dl serve
```

Server lokal akan aktif secara default di `http://127.0.0.1:8003`.

## 🌐 Deploy ke Render

Server ini dapat dideploy ke Render dengan konfigurasi environment berikut:

| Environment Variable / Flag | Fungsi |
|---|---|
| `PARTH_DL_ALLOW_REMOTE=1` | Mengizinkan server menerima koneksi dari host publik / cloud |
| `PARTH_DL_CORS_ORIGIN=https://xxx.vercel.app` | Mengizinkan domain Vercel frontend melakukan request CORS |
| `PARTH_DL_API_KEY=...` | (Opsional) Key autentikasi request API |

## 📁 Struktur Proyek Backend

- `parth_dl/core.py` — Logika inti pengunduhan media
- `parth_dl/extractors.py` — Extractor URL Instagram
- `parth_dl/server.py` — HTTP API Server + Handler
- `parth_dl/cli.py` — Entry point perintah CLI
- `parth_dl/web/` — Template UI internal
- `docs/` — Dokumentasi API & penggunaan

---
© 2026 InstaSave · Dibuat oleh Eva
