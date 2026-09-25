# parth-dl (backend)

Backend Python package untuk parth-dl — CLI + local HTTP API (`parth-dl serve`)
untuk download media Instagram publik (reels, post, carousel, foto profil).

Package ini masih membawa UI bawaannya sendiri di `parth_dl/web/` sehingga
perintah `parth-dl serve` tetap berjalan mandiri (backend menyajikan UI-nya
sendiri di satu origin yang sama). Folder `../frontend` di repo sebelah adalah
salinan UI yang sama, diekstrak untuk keperluan pengembangan/hosting terpisah
(lihat README di sana).

## Install & jalankan

```bash
pip install -e .
parth-dl serve
```

Server lokal aktif di `http://127.0.0.1:8003` secara default.

## Struktur

- `parth_dl/core.py` — logika download inti
- `parth_dl/extractors.py` — extractor URL Instagram
- `parth_dl/server.py` — HTTP API lokal + penyaji UI bawaan
- `parth_dl/cli.py` — entry point CLI
- `parth_dl/utils.py` — helper & error types
- `parth_dl/web/` — UI bawaan yang disajikan oleh `serve`
- `tests/` — test suite (pytest)

## Deploy ke Render (untuk dipasangkan dengan frontend di Vercel)

Secara default `parth-dl serve` **menolak** bind ke selain `127.0.0.1` /
`localhost` / `::1` — ini keputusan desain yang disengaja di upstream, karena
API ini bisa memicu download (baca: bandwidth & disk) dan awalnya dirancang
cuma untuk dipakai dari komputer sendiri. Supaya bisa dideploy publik di
Render, sudah ditambahkan mode eksplisit "remote" (harus dinyalakan manual,
default tetap perilaku lama):

| Env var / flag | Fungsi |
|---|---|
| `PARTH_DL_ALLOW_REMOTE=1` (`--allow-remote`) | Wajib diset supaya server mau bind ke `0.0.0.0`/host publik. Tanpa ini, start command akan langsung error. |
| `PARTH_DL_API_KEY=...` (`--api-key`) | **Sangat disarankan** kalau `ALLOW_REMOTE` aktif — tanpa ini, siapa pun yang tahu URL Render kamu bisa memicu download & menghabiskan resource server kamu. Frontend harus mengirim header `X-Api-Key` yang sama di setiap request. |
| `PARTH_DL_CORS_ORIGIN=https://xxx.vercel.app` (`--cors-origin`) | Origin frontend yang diizinkan memanggil API ini cross-origin. Isi persis domain Vercel kamu. |
| `PORT` | Otomatis diisi Render — kode sudah baca `$PORT` sebagai default port. |

### Langkah deploy

1. Push folder `backend/` ini sebagai repo GitHub sendiri.
2. Di Render: **New → Web Service**, connect ke repo itu (bisa pakai
   `render.yaml` yang sudah disediakan lewat "New → Blueprint", atau isi
   manual):
   - Build command: `pip install -e .`
   - Start command: `parth-dl serve --allow-remote --host 0.0.0.0 --no-open`
   - Environment: set `PARTH_DL_API_KEY` (generate string acak) dan
     `PARTH_DL_CORS_ORIGIN` (domain Vercel frontend kamu, isi setelah frontend
     dideploy).
3. Setelah live, catat URL-nya (`https://xxx.onrender.com`) — ini yang
   dipakai frontend sebagai `API_BASE`.

### Batasan penting di Render

- **Disk ephemeral**: kecuali kamu tambah *persistent disk* (butuh plan
  berbayar, lihat `render.yaml`), isi folder `downloads/` hilang tiap
  deploy/restart. Wajar untuk backend yang cuma jadi perantara download,
  tapi jangan dianggap penyimpanan permanen.
- **Instance free tier sleep**: request pertama setelah idle akan lambat
  (cold start).
- Kalau `PARTH_DL_API_KEY` tidak diset padahal `--allow-remote` aktif, server
  tetap jalan tapi akan mencetak warning ke log — API-nya publik tanpa proteksi.

Publish asli project ini: https://github.com/parthmax2/parth-dl
