# SambasKu Pronunciation

Repositori **penyimpanan file audio pelafalan** untuk aplikasi **SambasKu**
(Kamus Digital Sambas–Indonesia).

## Apa isi repo ini?

Audio di sini **bukan** konten editorial manual. File diunggah otomatis oleh
**backend SambasKu API** saat pengguna (admin web atau aplikasi mobile)
merekam / mengirim pelafalan kata atau contoh kalimat.

| Sumber submit | Cara |
| ------------- | ---- |
| Admin (web) | Rekam / unggah di form kata → `POST …/pronunciations/audio` |
| Mobile (Flutter) | Sheet rekam pelafalan → endpoint yang sama |
| Backend API | Menerima multipart → menulis file ke repo ini lewat **GitHub Contents API** |

Repo ini harus **publik** agar client bisa memutar file lewat URL raw GitHub.

## Struktur path

```text
assets/
└── audio/
    └── <dialect|umum>/
        └── <lemma-slug>/
            └── <ulid>.<ext>     # mp3 | m4a | wav | ogg | webm
```

Contoh:

```text
assets/audio/umum/makatn/01HXYZ….m4a
assets/audio/sambas-kota/makatn/01HABC….wav
```

- `dialect` — kode dialek (slug); jika tidak ada → folder `umum`
- `lemma-slug` — lemma dinormalisasi (huruf kecil, non-alfanumerik → `-`)
- Nama file — ULID unik (immutable; unggah baru = file baru)

## URL publik (dipakai client)

```text
https://raw.githubusercontent.com/iamutaki/sambasku-pronunciation/main/<path>
```

Contoh:

```text
https://raw.githubusercontent.com/iamutaki/sambasku-pronunciation/main/assets/audio/umum/makatn/01HXYZ….m4a
```

URL lengkap disimpan di baris tabel `word_audios.url` pada database SambasKu
(Turso / SQLite), bersama metadata (MIME, ukuran, penutur, `example_id`, dll.).

## Yang tidak dilakukan di repo ini

- Tidak ada UI upload manual yang didukung sebagai alur produksi
- Tidak menyimpan notasi IPA (itu tabel `pronunciations` di API)
- Jangan rename / pindah file yang sudah di-referensi DB — URL raw akan putus

## Akses API (server saja)

Backend memakai fine-grained PAT dengan izin **Contents: Read & Write** pada
repo ini. Variabel lingkungan (di API / Workers):

```env
PRONUNCIACION_PROVIDER=github
PRONUNCIACION_GITHUB_URL=https://github.com/iamutaki/sambasku-pronunciation
PRONUNCIACION_GITHUB_TOKEN=<pat>
```

Dokumentasi endpoint & kontrak: `docs/api/29-api-pronunciation-audio.md`
(di monorepo SambasKu) dan ringkasan di `api/README.md`.

## Lisensi / atribusi

Rekaman dikirim kontributor SambasKu untuk keperluan kamus. Metadata penutur
(`speaker_name`) ada di database API, bukan di path file.
