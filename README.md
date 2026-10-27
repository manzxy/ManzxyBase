<div align="center">

# ✦ ManzxyMD — ManzxySelf v8 ✦

**Bot WhatsApp modular berbasis Baileys** — Jadibot pairing code custom, tombol native flow universal,
Carousel, Rich HTML Webview, database user/grup, dan manajer plugin langsung dari chat.

`Node.js ≥ 18` • `CommonJS` • `Low-RAM friendly (±350 MB)` • `Hot-reload plugin`

</div>

---

## 📑 Daftar Isi

1. [Fitur Utama](#-fitur-utama)
2. [Instalasi Cepat](#-instalasi-cepat)
3. [Konfigurasi (`config.js`)](#-konfigurasi-configjs)
4. [Menjalankan Bot](#-menjalankan-bot)
5. [Daftar Command](#-daftar-command)
6. [Jadibot (detail)](#-jadibot)
7. [Plugin Manager dari Chat](#-plugin-manager-dari-chat-svplug--delplug--editplug--getplug)
8. [Menulis Plugin](#-menulis-plugin)
9. [Tombol, Carousel & Rich HTML](#-tombol-carousel--rich-html)
10. [Struktur Database](#-struktur-database)
11. [Struktur Folder](#-struktur-folder)
12. [Console & Log](#-console--log)
13. [Daftar Perbaikan (Changelog v8)](#-daftar-perbaikan-changelog-v8)
14. [Troubleshooting](#-troubleshooting)
15. [Keamanan](#-keamanan)

---

## 🚀 Fitur Utama

| Fitur | Keterangan |
|---|---|
| 🤖 **Jadibot** | Pairing code custom `MANZKENZ`, reconnect benar (515/428/401), auto-restore saat restart, masa sewa, batas slot |
| 🔘 **Tombol universal** | Native flow + node `biz` v9 untuk WA biasa, Bisnis, dan clone; otomatis fallback teks bila gagal |
| 🎠 **Carousel** | Kartu geser berisi gambar + tombol (1–10 kartu) |
| 🌐 **Rich HTML** | HTML kustom di-host server bot lalu dibuka di Webview WhatsApp |
| 📚 **Menu** | `.menu` (tombol kategori), `.allmenu` (semua fitur), `.kategori`, otomatis mengikuti plugin |
| 🗄️ **Database** | JSON atomik + backup, struktur user/grup/jadibot lengkap, reset limit harian |
| 🧩 **Plugin manager** | `.svplug` `.delplug` `.editplug` `.getplug` `.listplug` — tanpa bolak-balik ke VPS |
| 🖥️ **Console detail** | Banner ASCII, kotak info sistem, log command lengkap, kode alasan disconnect |
| ⚙️ **Low RAM** | Antrean command, cache grup, monitor memori, pembersih sesi aman |

---

## ⚡ Instalasi Cepat

```bash
# 1. Ekstrak proyek, masuk foldernya
cd ManzxySelf

# 2. Pasang dependency
npm install

# 3. Edit config.js  →  WAJIB ganti ownerNumber
nano config.js

# 4. Jalankan (pairing code akan ditanya di terminal)
npm start
```

Untuk VPS dengan **PM2**:

```bash
npm i -g pm2
PAIRING_NUMBER=628xxxxxxxxxx pm2 start ecosystem.config.js
pm2 logs ManzxyMD
pm2 save && pm2 startup
```

> ⚠️ Jalankan **hanya 1 proses** per sesi WhatsApp. Dua proses dengan sesi yang sama saling menendang
> (`440 Connection Replaced`).

---

## 🔧 Konfigurasi (`config.js`)

| Kunci | Default | Fungsi |
|---|---|---|
| `ownerNumber` | `['6281234567890']` | **Root Owner** (tanpa `+`). Hanya mereka yang boleh `.eval`, `.svplug`, dst. |
| `prefix / prefixes / multiPrefix / noPrefix` | `.` + `! # / ~` | Pengaturan prefix |
| `selfMode` | `true` | `true` = hanya owner & akun bot sendiri yang bisa memakai bot |
| `usePairingCode` | `true` | `false` = login pakai QR |
| `pairingNumber` | `env PAIRING_NUMBER` | Nomor bot (agar tidak ditanya di terminal / PM2) |
| `customPairingCode` | `'MANZKENZ'` | Kode pairing custom, **tepat 8 karakter** A–Z/0–9 tanpa `I L O U`. `''` = acak |
| `jadibot.max` | `10` | Maks sesi jadibot bersamaan |
| `jadibot.autoRestore` | `true` | Sesi tersimpan otomatis menyala lagi saat bot restart |
| `jadibot.pairingTimeoutSec` | `180` | Batas waktu user memasukkan kode |
| `jadibot.maxReconnect` | `10` | Batas gagal reconnect berturut-turut |
| `jadibot.allowPublicUsers` | `true` | User biasa boleh jadibot (hanya nomor miliknya) saat mode PUBLIC |
| `database.path` | `./database/db.json` | Lokasi database |
| `database.defaultLimit` | `30` | Limit harian user baru |
| `button.botNode` | `true` | Node `<bot biz_bot=1>` agar tombol tampil di chat **pribadi**. Matikan bila tombol justru hilang |
| `button.fallbackText` | `true` | Kirim versi teks bila tombol gagal |
| `console.*` | lihat file | `logCommands`, `logAllMessages`, `stack`, `debug`, `clear` |
| `publicUrl` | `env PUBLIC_URL` | URL publik VPS untuk Rich HTML (disarankan **HTTPS**) |
| `ignoreOldSeconds` | `90` | Pesan lebih lama dari ini diabaikan (cegah command lama terulang saat bot baru hidup) |
| `categoryMeta` | — | Ikon, label, dan urutan kategori di menu |

---

## ▶️ Menjalankan Bot

| Perintah | Fungsi |
|---|---|
| `npm start` | Jalan sesuai `usePairingCode` di config |
| `npm run pairing` | Paksa login pakai pairing code |
| `npm run qr` | Paksa login pakai QR |
| `npm run check` | Cek sintaks file inti |

Setelah login pertama, sesi tersimpan di `session/`. Bila akun logout dari HP, folder sesi dihapus
otomatis dan bot meminta pairing baru.

---

## 📜 Daftar Command

Ikon 🔒 = khusus **Root Owner**. Daftar selalu terbaru lewat `.allmenu`.

### 🏠 Main
| Command | Fungsi |
|---|---|
| `.menu [kategori\|all]` | Menu utama dengan tombol kategori |
| `.allmenu` (`.menuall` `.fullmenu`) | **Semua fitur** per kategori |
| `.kategori <nama>` | Isi satu kategori |
| `.owner` | Kontak owner |
| `.profile` | Profil, level, limit, statistik dari database |

### 🤖 Jadibot
| Command | Fungsi |
|---|---|
| `.jadibot <nomor> [durasi]` | Minta kode pairing. Durasi (owner saja): `30m` `12h` `7d` `2w` `1mo` `1y` |
| `.listjadibot` | Daftar sesi, status, uptime, masa sewa (owner) |
| `.stopjadibot [nomor]` | Hentikan (sesi tetap tersimpan) |
| `.deljadibot [nomor]` | Hentikan + hapus sesi permanen |

### 🎛️ Interactive
`.allbutton` • `.carousel` • `.rich` • `.richhtml [url\|<html>]`

### 👥 Group / 🛠️ Tools
`.hidetag <teks>` • `.ping`

### 🧩 Plugin 🔒
`.svplug` • `.delplug` • `.editplug` • `.getplug` • `.listplug`

### 👑 Owner
| Command | Fungsi |
|---|---|
| `.self` / `.public` / `.mode` | Mode bot (jadibot: per sesi & tersimpan) |
| `.ban` / `.unban <nomor>` | Ban user (juga lewat reply) |
| `.mute` / `.unmute` | Bot diam / aktif di grup |
| `.reload` 🔒 | Muat ulang semua plugin |
| `.eval` 🔒 | Jalankan kode JS |
| `.clearsession` 🔒 | Bersihkan sampah aman + cache RAM |
| `.dbinfo [user <no>\|group]` 🔒 | Statistik / isi database |

---

## 🤖 Jadibot

### Alur
```
.jadibot 628xxx
   └─ bot membuat sesi di sessions_jadibot/628xxx
   └─ meminta kode pairing (custom: MANZKENZ → tampil MANZ-KENZ)
   └─ kode dikirim ke pemohon (di grup → lewat chat pribadi)
   └─ user memasukkan kode di WhatsApp: Perangkat Tertaut › Tautkan dengan nomor telepon
   └─ WhatsApp memutus koneksi (515 "restart required")  ← NORMAL
   └─ bot membuat socket baru memakai kredensial yang sama
   └─ connection "open" → pemohon diberi tahu "tersambung"
```

### Penanganan kode disconnect
| Kode | Arti | Tindakan bot |
|---|---|---|
| `515` | Restart required | Reconnect 0,8 dtk (tidak dihitung gagal) |
| `401` `403` `411` | Logout / ditolak | Sesi **dihapus**, pemohon diberi tahu, **tanpa reconnect** |
| `440` | Digantikan sesi lain | Berhenti (tidak berebut sesi) |
| `408` `428` `500` dll | Putus sementara | Reconnect backoff 2 → 30 dtk, menyerah setelah `maxReconnect` |
| putus saat belum pairing | Kode kedaluwarsa | Sesi setengah jadi dibuang, minta `.jadibot` ulang |

### Hak akses
* Owner jadibot (pemilik nomor) hanya jadi **owner di botnya sendiri**.
* Perintah berbahaya (`eval`, `svplug`, `reload`, …) hanya untuk **Root Owner** di `config.js` —
  pemilik jadibot **tidak** bisa menjalankan kode di server Anda.
* `jadibot` tidak bisa dijalankan dari dalam jadibot lain.

### Sewa bot
`.jadibot 628xxx 30d` → otomatis berhenti & sesi dihapus saat masa sewa habis (dicek tiap 5 menit),
pemilik diberi notifikasi. Info tersimpan di database (`jadibots[nomor].expire`).

---

## 🧩 Plugin Manager dari Chat (`svplug` / `delplug` / `editplug` / `getplug`)

Semua command ini **Root Owner only**, otomatis: validasi sintaks → backup → tulis → muat ulang →
**rollback bila gagal dimuat**. Backup ada di `backup_plugins/` (15 versi terakhir per plugin).

### `.svplug` — simpan / timpa plugin
```text
# A. Reply pesan berisi kode (atau file .js) lalu ketik:
.svplug tools/halo

# B. Tulis langsung (baris pertama nama, sisanya kode):
.svplug tools/halo
module.exports = {
    command: ['halo'],
    category: 'tools',
    description: 'Sapa',
    async execute(sock, m, { reply }) { await reply('Halo!'); }
};

# C. Nama boleh kosong → dibaca dari nama file / isi kode:
.svplug            (reply file cek.js  atau  reply kode)
```
Kategori baru (folder) dibuat otomatis. Bila nama sudah ada, plugin **diperbarui** dan versi lama di-backup.

### `.editplug` — edit tanpa ganti seluruh file
```text
# Ganti sebagian teks
.editplug ping
[CARI]
Cek kecepatan
[GANTI]
Tes kecepatan

# Timpa seluruh isi: reply kode / file .js
.editplug ping

# Lihat isi saat ini
.editplug ping
```
Teks pencarian harus **unik**; tambah `--all` untuk mengganti semua kemunculan.

### `.getplug` — ambil file
| Perintah | Hasil |
|---|---|
| `.getplug` | Daftar plugin |
| `.getplug ping` | Kirim `ping.js` sebagai dokumen |
| `.getplug ping --text` | Kirim sebagai teks |
| `.getplug ping --backup` | Versi backup terbaru (cara memulihkan plugin yang terhapus) |

Alur kerja nyaman: `.getplug x` → edit file di HP → reply file itu dengan `.svplug`.

### `.delplug` & `.listplug`
`.delplug <nama>` memindahkan plugin ke backup (bisa dipulihkan). Plugin manajemen (`svplug`, `delplug`,
`editplug`, `getplug`, `listplug`) dilindungi agar Anda tidak terkunci. `.listplug` menampilkan semua file + command.

Nama bisa berupa `kategori/nama`, nama file, atau nama command (mis. `.getplug hai`).

---

## 🛠️ Menulis Plugin

Taruh file `.js` di `plugins/<kategori>/`. Bot memuat otomatis dan **hot-reload** saat file berubah.

```js
module.exports = {
    command: ['halo', 'hai'],        // wajib (array / string)
    category: 'tools',               // opsional; default nama folder
    description: 'Menyapa user',     // tampil di menu
    usage: '.halo <nama>',           // opsional; tampil di menu
    // ── pembatas akses (semua opsional) ──
    rootOwner: false,                // hanya Root Owner (config.ownerNumber)
    owner: false,                    // owner bot / pemilik jadibot
    group: false,                    // hanya di grup
    private: false,                  // hanya chat pribadi
    admin: false,                    // hanya admin grup
    botAdmin: false,                 // bot harus admin
    order: 50,                       // urutan di menu (kecil = atas)

    async execute(sock, m, ctx) {
        const { from, sender, pushname, args, text, prefix, reply, user, group, db, isOwner, isAdmin } = ctx;
        await reply(`Halo ${pushname}!`);
    },

    // hook opsional: jalan untuk SETIAP pesan; return true = hentikan pemrosesan
    async before(m, ctx) { return false; }
};
```

### Isi `ctx`
`from` `sender` `senderNumber` `pushname` `body` `mtype` `quoted` `args` `text` (**mempertahankan baris baru**)
`command` `prefix` `isGroup` `groupMetadata` `groupName` `isOwner` `isRootOwner` `isAdmin` `isBotAdmin`
`isSelfMode` `user` `group` `db` `config` `reply()` `getPluginsByCategory()` `loadPlugins()`

> Error di dalam plugin **tidak** membuat bot mati/diam: dicatat di console lengkap dengan nama plugin,
> dan pengirim menerima pesan error singkat.

---

## 🔘 Tombol, Carousel & Rich HTML

```js
const { button } = require('../../lib/richHelper');

// Tombol
await sock.sendUniversalButton(from, {
    title: 'Judul', body: 'Isi', footer: 'Footer', image: { url: 'https://…' }
}, [
    button.quick('Menu', '.menu'),
    button.url('Website', 'https://example.com', true),   // true = webview
    button.copy('Salin', 'KODE123'),
    button.list('Pilih', [{ title: 'Bagian', rows: [{ title: 'Ping', description: '…', id: '.ping' }] }])
], m);

// Carousel (1–10 kartu)
await sock.sendCarousel(from, { body: 'Geser ➜' }, [
    { title: 'Kartu 1', body: '…', image: { url: '…' }, buttons: [button.quick('Pilih', '.menu')] }
], m);

// Rich HTML — HTML kustom (butuh publicUrl) atau URL langsung
await sock.sendRichHtml(from, { title: 'Halaman', html: '<h1>Halo</h1>' }, m);
await sock.sendRichHtml(from, { title: 'Web', url: 'https://example.com' }, m);

// Thumbnail besar
await sock.sendRichText(from, 'Teks', { title: '…', body: '…', thumbnailUrl: '…' }, m);
```

* Upload gambar gagal → pesan tetap dikirim tanpa gambar.
* Tombol gagal dikirim → otomatis dikirim versi teks (`button.fallbackText`).
* **Rich HTML**: set `PUBLIC_URL` (mis. `https://bot.domain.com` → reverse proxy ke port bot).
  Halaman disimpan di memori 1 jam (maks 50 halaman, 300 KB/halaman).

> ℹ️ Tampilan tombol/carousel ditentukan server & aplikasi WhatsApp, yang sering berubah. Jika di versi
> WhatsApp tertentu tombol tidak muncul di **chat pribadi**, coba `button.botNode: false`; jika tidak muncul
> di grup, cek log `[BUTTON]` / `[CAROUSEL]` di console.

---

## 🗄️ Struktur Database

File: `database/db.json` (JSON atomik: tulis `.tmp` → rename; salinan `db.json.bak`; bila rusak otomatis
dipulihkan dari `.bak`). Field yang hilang di data lama **dilengkapi otomatis** (migrasi aman) saat data dibaca.

```text
db.json
├── meta        { schemaVersion, createdAt, updatedAt }
├── settings    { bannedWords[], maintenance, totalCommandsRun }
├── users       { "<nomor>": User }
├── groups      { "<jid grup>@g.us": Group }
├── jadibots    { "<nomor>": Jadibot }
└── stats       { messages, commands, startCount, commandUsage{cmd: n} }
```

### `User` (kunci = nomor tanpa `+`)
| Field | Tipe | Keterangan |
|---|---|---|
| `number` `jid` `lid` | string | Identitas (`lid` = ID format baru WhatsApp) |
| `name` `names[]` | string | Nama terakhir & 5 riwayat nama |
| `registered` `regTime` `age` | bool/num | Registrasi (disiapkan untuk plugin) |
| `role` | string | `user` `premium` `owner` `banned` |
| `premium` | `{active, expire, since}` | Premium; otomatis nonaktif saat `expire` lewat |
| `banned` | `{active, reason, by, time}` | **Ditegakkan**: user di-ban diabaikan |
| `limit` | `{max, used, lastReset}` | Limit harian, reset otomatis tiap hari (zona waktu bot) |
| `level` `exp` | number | Naik otomatis: +5 exp per command, naik level tiap `level×100` exp |
| `money` `bank` | number | Saldo (untuk plugin ekonomi) |
| `warn` | number | Jumlah peringatan |
| `afk` | `{active, since, reason}` | Status AFK |
| `stats` | `{messages, commands, lastCommand, lastCommandAt, firstSeen, lastSeen}` | **Dihitung otomatis** |
| `commandUsage` | `{cmd: n}` | Pemakaian per command |
| `settings` | `{language, notify}` | Preferensi |
| `jadibot` | `{owned[], maxOwned}` | Jadibot milik user |
| `createdAt` `updatedAt` | ms epoch | |

### `Group` (kunci = JID grup)
| Field | Keterangan |
|---|---|
| `id` `name` `owner` `description` `size` | Diisi otomatis dari metadata grup |
| `banned` `{active, reason, time}` | **Ditegakkan**: grup di-ban diabaikan |
| `rent` `{active, since, expire, by}` | Sewa bot per grup |
| `settings.mute` | **Ditegakkan** — bot diam (kecuali owner/admin) · `.mute` `.unmute` |
| `settings.adminOnly` | **Ditegakkan** — hanya admin yang bisa command |
| `settings.prefix` | **Ditegakkan** — prefix khusus grup (`''` = ikut config) |
| `settings.welcome` `welcomeText` `bye` `byeText` | Disiapkan untuk plugin sambutan |
| `settings.antilink` `{enabled, mode, whitelist[]}` | Disiapkan untuk plugin (mode `delete`/`warn`/`kick`) |
| `settings.antibadword` `{enabled, words[]}` · `antispam` `{enabled, maxPerMinute}` · `antiviewonce` · `antidelete` · `rules` · `language` | Disiapkan untuk plugin |
| `disabledCommands[]` | **Ditegakkan** — command yang dimatikan di grup |
| `warns` `{nomor: n}` | Peringatan per anggota |
| `stats` `{messages, commands, lastActive, topUsers{nomor: n}}` | **Dihitung otomatis** |

### `Jadibot` (kunci = nomor)
`{ number, requestedBy, createdAt, lastConnected, expire (0 = permanen), selfMode, status, reconnects }`
dengan `status` ∈ `pairing` `connecting` `open` `closed` `failed` `expired`.

### Memakai database di plugin
```js
async execute(sock, m, { user, group, db, reply }) {
    user.money += 100;            // ubah langsung
    db.markDirty();               // jadwalkan simpan (debounce 3 dtk)
    const u = db.getUser('628123456789');          // ambil/buat user lain
    const g = db.getGroup('1203xxxx@g.us');       // ambil/buat grup
}
```
> Field bertanda "Disiapkan untuk plugin" sudah ada di struktur & dilengkapi otomatis, tetapi logikanya
> (mis. kirim pesan sambutan) perlu plugin tersendiri — struktur sudah siap dipakai.

---

## 📁 Struktur Folder

```text
ManzxySelf/
├── index.js                 # Boot, koneksi utama, reconnect, shutdown aman
├── handler.js               # Parsing pesan, identitas (LID), hak akses, eksekusi plugin
├── config.js                # Seluruh pengaturan
├── ecosystem.config.js      # PM2
├── lib/
│   ├── jadibotManager.js    # Sesi jadibot: pairing, reconnect, restore, sewa
│   ├── richHelper.js        # Tombol, carousel, rich html, rich text
│   ├── database.js          # Database user/grup/jadibot
│   ├── pluginLoader.js      # Muat plugin + hot-reload
│   ├── pluginManager.js     # Logika svplug/delplug/editplug/getplug
│   ├── menuBuilder.js       # Pembuat menu
│   ├── htmlServer.js        # HTTP: keep-alive & hosting Rich HTML
│   ├── lowRamOptimizer.js   # Cache, antrean, monitor memori
│   ├── logger.js            # Banner & console
│   └── utils.js
├── plugins/
│   ├── main/ jadibot/ interactive/ group/ tools/ owner/ plugin/
├── database/                # db.json (+ .bak)
├── backup_plugins/          # backup otomatis plugin
├── session/                 # sesi bot utama   (jangan dibagikan!)
└── sessions_jadibot/        # sesi tiap jadibot (jangan dibagikan!)
```

---

## 🖥️ Console & Log

* **Banner** ASCII, kotak **SYSTEM** (Node, Baileys, RAM bot/VPS, PID) dan **KONFIGURASI**.
* **BOT ONLINE**: akun, jumlah plugin/command, jumlah per kategori, jadibot aktif, isi database.
* Setiap command → kotak: pengirim (+ label OWNER/USER), chat (grup/pribadi), bot (MAIN / JADIBOT +nomor),
  perintah + argumen, waktu proses, RAM, panjang antrean, status `OK` / `ERROR` / `DITOLAK`.
* Disconnect ditulis lengkap dengan kode dan artinya (`428 Connection Closed`, `515 Restart Required`, …).
* Error plugin menampilkan nama plugin + 3 baris stack. Matikan lewat `console.stack: false`.
* `console.logAllMessages: true` untuk satu baris per pesan masuk; `console.debug: true` untuk log teknis.

---

## 🛠️ Daftar Perbaikan (Changelog v8)

### Jadibot — akar masalah "tidak bisa masuk"
1. **Deteksi logout salah**: `(error instanceof Boom)?.output?.statusCode` selalu `undefined`
   → bot menganggap semua putus koneksi boleh reconnect (termasuk logout → loop tanpa henti). Sekarang
   membaca `error.output.statusCode` dengan benar dan setiap kode ditangani sendiri.
2. **Reconnect setelah pairing selalu gagal**: setelah kode dimasukkan, WhatsApp memutus koneksi (515).
   Kode lama memanggil `startJadibot()` lagi, tetapi sesi masih ada di `Map` → ditolak "sudah aktif",
   dan error ditelan `.catch(() => {})`. Sekarang reconnect memakai fungsi internal `connect()`.
3. **Kode pairing palsu**: `MANZKENZ-XXXX-XXXX` hanyalah kode acak yang ditempeli awalan, sehingga tidak
   valid saat diketik. Sekarang memakai pairing code **custom asli** (8 karakter `MANZKENZ`) dengan
   fallback ke kode acak.
4. Permintaan kode hanya `setTimeout(3000)` tanpa retry → sekarang menunggu event `qr`, retry 4×.
5. Sesi setengah jadi tak pernah dibersihkan → ada timeout pairing & pembersihan.
6. Tidak ada pemulihan setelah restart → `autoRestore`.
7. Pemilik jadibot bisa `.eval` (menjalankan kode di server Anda) → sekarang hanya Root Owner.
8. `.listjadibot` selalu menampilkan "Public" (field `selfMode` tidak pernah dikirim) → diperbaiki;
   mode jadibot kini tersimpan di database.

### Tombol / Carousel / Rich HTML
* Carousel: `messageVersion: 1` tidak ada; kini ditambahkan, plus node `biz` & `bot`, validasi 1–10 kartu,
  tombol default bila kartu tanpa tombol, dan fall
