<div align="center">

<img src="https://lunara.softbotz.my.id/f/NfVzyYdyvwQ5.jpeg" width="320" alt="ManzxyMD">

# ManzxyMD v8.9.1

Bot WhatsApp modular berbasis Baileys — jadibot, sewa bot, premium, plugin manager dari chat.

`Node.js >= 18` · `CommonJS` · `Baileys (WA Web terbaru otomatis)`

</div>

---

## Info Script

| | |
|---|---|
| Nama / versi | ManzxyMD (ManzxySelf) 8.9.1 |
| Runtime | Node.js 18+ (disarankan 20/22) |
| Library utama | `@whiskeysockets/baileys`, `@napi-rs/canvas`, `sharp` (opsional), `axios` |
| Fitur | Jadibot, sewa bot, premium & limit, daftar, welcome/bye, deteksi perubahan grup, polling, tombol native flow, plugin manager, ping canvas |
| Data | JSON lokal di `database/db.json` (auto-backup) |

## Update Terbaru

**v8.9.1**
- Kartu dokumen + saluran hanya untuk balasan `sock.reply`; menu (tombol) dan `.ping` (gambar + tombol) kembali normal.
- Menu lebih ringkas, emoji dikurangi.
- Command baru `.setch <link saluran>` untuk mengatur nama & ID saluran dari chat.
- Thumbnail bot diganti (`config.thumbnailUrl`), `.ping` memakai gambar yang sama dan hanya render 1x.
- Alias `.ytplay` yang bentrok antara `play` dan `ytsearch` dirapikan.

**v8.9.0**
- Fix error `Cannot read properties of undefined (reading 'attrs')` saat bot lama berjalan (`.ping`, `.getplug`, kirim media) di bot utama & jadibot. Penyebab: koneksi upload media Baileys "keracunan" setelah satu kali gagal. `lib/connectionGuard.js` mendeteksinya dan menyambung ulang socket otomatis, plus pemeriksa ringan tiap 5 menit.
- Versi WhatsApp terbaru otomatis (`lib/waVersion.js`), di-cache 30 menit, diperbarui saat reconnect.
- Balasan bot bergaya forward + saluran (dokumen bergambar, "Diteruskan berkali-kali", tombol Lihat saluran).
- Perintah yang diketik pemilik di saluran miliknya atau diposting di status akun bot ikut dijalankan.

## Instalasi

```bash
# 1. ekstrak zip lalu masuk foldernya
cd ManzxyBaseMD

# 2. pasang dependensi
npm install

# 3. isi config.js (minimal: ownerNumber, selfMode, prefix, kipay.api_key)

# 4. jalankan
npm run pairing     # login dengan kode pairing (isi pairingNumber di config.js atau ketik saat diminta)
npm run qr          # atau login dengan QR
npm start           # jalan biasa setelah sesi tersimpan
```

PM2 / panel: gunakan `ecosystem.config.js` (`pm2 start ecosystem.config.js`).

Setelah bot hidup: ketik `.setch <link saluran WhatsApp>` agar nama saluran muncul di kartu balasan.

## Konfigurasi Penting (`config.js`)

| Kunci | Fungsi |
|---|---|
| `ownerNumber` | Nomor owner (format 628xxx) |
| `selfMode` | `true` hanya owner, `false` publik |
| `prefix` / `prefixes` / `noPrefix` | Awalan command |
| `replyStyle` | `forward` (kartu dokumen + saluran), `ad` (externalAdReply), `plain` |
| `channel`, `card` | Saluran & tampilan dokumen di kartu (juga bisa lewat `.setch`) |
| `menu.useButtons` | `true` menu bertombol, `false` menu teks dalam kartu |
| `thumbnailUrl`, `footerText` | Gambar & footer bot |
| `guard` | Penjaga koneksi (interval cek, batas gagal) |
| `waVersion` | Kosong = otomatis; paksa manual `[2, 3000, xxxxxxxxxx]` |
| `jadibot`, `sewa`, `premium`, `limit` | Pengaturan fitur masing-masing |
| `hotReload` | Hot-reload plugin (bawaan mati) |

Kunci yang belum ada di `config.js` lama otomatis memakai nilai bawaan dari `lib/configDefaults.js`.

## Struktur Folder

```
index.js               koneksi bot utama
config.js              semua pengaturan
handler/
  message.js           alur satu pesan -> plugin
  events.js            semua event socket (bot utama + jadibot)
  parser.js            baca semua jenis pesan + prefix/command
  access.js            identitas pengirim, aturan wajib daftar
  texts.js             semua kalimat balasan handler
  groupDetect.js poll.js antiCall.js
lib/
  richHelper.js        sock.reply, kartu, tombol, carousel, polling
  connectionGuard.js   anti error media + sambung ulang otomatis
  waVersion.js         versi WhatsApp Web terbaru
  jadibotManager.js    kelola sesi jadibot
  pluginLoader.js pluginManager.js   muat & kelola plugin
  database.js botSettings.js rental.js renewal.js payment.js cleaner.js ...
plugins/<kategori>/<nama>.js   semua command
database/              db.json (+ backup)
assets/fonts/          font opsional untuk canvas
sessions/              sesi bot utama (otomatis)
sessions_jadibot/      sesi jadibot (otomatis)
```

## Tata Letak Plugin

Satu file = satu fitur, simpan di `plugins/<kategori>/`. Nama folder = kategori di menu (kategori baru muncul otomatis).

```js
module.exports = {
    name: 'Contoh',                 // nama fitur
    command: ['contoh', 'cth'],     // command + alias
    category: 'tools',              // sebaiknya sama dengan nama folder
    description: 'Deskripsi singkat',
    usage: '.contoh <teks>',
    limit: 1,                       // biaya limit (0 = gratis)
    // opsional: premium, owner, rootOwner, group, private, admin, botAdmin, register: true
    async execute(sock, m, { args, text, reply, prefix, isOwner, user }) {
        await reply('Halo!');
    }
};
```

- Balasan: `reply(teks)` (kartu), `sock.reply`, `sock.sendUniversalButton`, `sock.sendCarousel`, `sock.sendPoll`.
- Hook semua pesan: tambah `before(m, ctx)`; event socket: `events: { 'group-participants.update': fn }`.
- Tambah plugin dari chat: `.svplug`, `.editplug`, `.getplug`, `.delplug`, `.listplug` (Root Owner).
- Contoh siap salin: `plugins/premium/vip.js` (premium), `plugins/main/daftar.js` (wajib daftar).

## Daftar Command Singkat

| Kategori | Command |
|---|---|
| main | `.menu` `.allmenu` `.kategori` `.profile` `.daftar` `.owner` `.batalbayar` |
| tools | `.ping` `.runtime` `.hd` `.tourl` `.gist` `.ytsearch` `.cekidch` |
| downloader | `.play` `.tiktok` |
| ai | `.imagine` |
| group | `.welcome` `.detect` `.hidetag` |
| jadibot | `.jadibot` `.listjadibot` `.stopjadibot` |
| sewa | `.sewabot` `.listsewa` |
| premium | `.buyprem` `.perpanjang` `.vip` `.amverify` |
| interactive | `.allbutton` `.carousel` `.rich` |
| plugin | `.svplug` `.getplug` `.editplug` `.delplug` `.listplug` |
| info | `.totalfitur` |
| owner | `.self` `.public` `.setting` `.setch` `.addprem` `.delprem` `.ban` `.reload` `.eval` `.exec` `.backupsc` `.clearsession` `.dbinfo` `.svfl` `.gtfl` |

Daftar lengkap & alias selalu ada di `.allmenu`.

## Troubleshooting Singkat

| Masalah | Solusi |
|---|---|
| Media/gambar error setelah lama jalan | Sudah ditangani `connectionGuard` (sambung ulang otomatis). Pastikan Baileys terbaru: `npm i @whiskeysockets/baileys@latest` |
| Nama saluran tidak muncul di kartu | Jalankan `.setch <link saluran>` |
| `.ping` tanpa gambar | `npm i @napi-rs/canvas` |
| Thumbnail kartu tidak tampil di WA orang lain | Pasang `sharp` (`npm i sharp`) agar gambar diperkecil <= 40 KB |
| Sesi digantikan / logout | Pastikan hanya 1 proses bot; login ulang dengan `npm run pairing` |

## Keamanan

Jangan bagikan folder `sessions/`, `sessions_jadibot/`, `database/` dan `kipay.api_key`. Jangan unggah ke repo publik.
