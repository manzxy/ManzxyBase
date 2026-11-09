# ManzxyMD

<div align="center">

<img src="assets/banner.svg" alt="ManzxyMD" width="100%">

**WhatsApp Bot berbasis [Baileys](https://github.com/WhiskeySockets/Baileys) & Node.js — arsitektur plugin, CommonJS.**

Bot WhatsApp modular dengan sistem plugin, jadibot (multi-sesi), sewa grup, langganan premium,
pembayaran QRIS, permainan, serta panel web dengan telemetri server.

![Node.js](https://img.shields.io/badge/Node.js-%E2%89%A518-339933?logo=node.js&logoColor=white)
![Baileys](https://img.shields.io/badge/Baileys-latest-25D366?logo=whatsapp&logoColor=white)
![Module](https://img.shields.io/badge/module-CommonJS-f7df1e)
![Version](https://img.shields.io/badge/version-8.11.1-blue)
![Plugins](https://img.shields.io/badge/plugins-95%20file%20%C2%B7%20311%20command-FF5DA2)
![Panel](https://img.shields.io/badge/panel-neobrutalism-3D8BFF)
![License](https://img.shields.io/badge/license-Unlicensed-lightgrey)

</div>

> [!WARNING]
> Gunakan bot ini hanya pada akun, grup, dan pengguna yang Anda kelola serta dengan izin pemiliknya.
> Otomatisasi WhatsApp di luar ketentuan resmi dapat menyebabkan pembatasan atau pemblokiran akun.
> Tanggung jawab penggunaan sepenuhnya berada pada operator instalasi.

---

## Daftar Isi

1. [Ringkasan](#ringkasan)
2. [Persyaratan](#persyaratan)
3. [Instalasi](#instalasi)
4. [Konfigurasi](#konfigurasi)
5. [Menjalankan Bot](#menjalankan-bot)
6. [Alur Pesan](#alur-pesan)
7. [Fitur dan Perintah](#fitur-dan-perintah)
8. [Susunan Proyek](#susunan-proyek)
9. [Panel Web (Jadibot)](#panel-web-jadibot)
10. [Telemetri Server](#telemetri-server)
11. [Mengembangkan Plugin](#mengembangkan-plugin)
12. [Pengujian](#pengujian)
13. [Deployment di Server](#deployment-di-server)
14. [Keamanan dan Penanganan Data](#keamanan-dan-penanganan-data)
15. [Troubleshooting](#troubleshooting)
16. [Kontribusi](#kontribusi)
17. [Lisensi dan Atribusi](#lisensi-dan-atribusi)

---

## Ringkasan

ManzxyMD memisahkan **inti proses** (koneksi WhatsApp, sesi, penyimpanan data, penerusan pesan)
dari **perintah** yang tersimpan sebagai plugin independen. Pendekatan ini memungkinkan penambahan,
penggantian, atau penghapusan fitur tanpa menyentuh `index.js`.

```text
    ┌──────────────┐   ┌──────────────┐   ┌──────────────┐
    │   WhatsApp   │──▶│   handler/   │──▶│   plugins/   │
    │   (Baileys)  │   │  filter/akses│   │  per kategori│
    └──────────────┘   └───────┬──────┘   └───────┬──────┘
                               │                  │
                        ┌──────▼──────────────────▼──────┐
                        │   lib/  (database, jadibot,    │
                        │   payment, rpg, sysinfo, web)  │
                        └───────────────┬────────────────┘
                                        │
                        ┌───────────────▼────────────────┐
                        │  Panel Web  ·  /panel          │
                        │  dashboard · monitor · sistem  │
                        └────────────────────────────────┘
```

**Highlight:**

- **Plugin-first** — setiap kategori fitur adalah folder di `plugins/`.
- **Jadibot multi-sesi** — tautkan beberapa akun WhatsApp tambahan dari dalam chat.
- **Monetisasi** — premium, sewa grup, jadibot, dan tagihan QRIS lewat KiPay.
- **Hot reload** — supervisor `scripts/dev.js` memantau perubahan source dan memulai ulang proses.
- **Persistensi JSON** — database tertunda dengan backup `.bak` otomatis.
- **UI kaya** — tombol native, carousel, polling, dan `externalAdReply`.
- **Panel web** — dashboard, monitor jadibot, topup QRIS, dan telemetri server dengan grafik.

---

## Persyaratan

| Kebutuhan | Keterangan |
| --- | --- |
| **Node.js** | **≥ 18** (diuji pada Node 22) — wajib untuk sintaks dan API Baileys terkini. |
| **npm** | Disertakan bersama Node.js. |
| **Akun WhatsApp** | Nomor yang akan ditautkan sebagai bot (disarankan nomor khusus, bukan nomor pribadi utama). |
| **Jaringan** | Akses ke server WhatsApp dan layanan pihak ketiga yang dipakai plugin tertentu. |
| **`sharp`** (opsional) | Dibutuhkan untuk pembuatan stiker dan konversi stiker diam → gambar. |
| **`@napi-rs/canvas`** | Dipakai untuk beberapa keluaran bergambar (welcome card, dsb). |

---

## Instalasi

```bash
git clone <url-repositori-anda> ManzxyBase
cd ManzxyBase
npm ci
```

Jika `package-lock.json` tidak tersedia, gunakan `npm install`.

Sebelum menautkan akun, **wajib** menyesuaikan `config.js`:

1. Ganti `ownerNumber` dengan nomor Anda (format angka dengan kode negara tanpa `+`, contoh `6281234567890`).
2. Tentukan `selfMode`:
   - `true` → hanya owner yang mendapat respons (cocok untuk pengujian).
   - `false` → akses publik sesuai aturan tiap plugin.
3. Tinjau `kipay.api_key` — jangan menyimpan kredensial nyata di source (gunakan env `KIPAY_API_KEY`).

> [!IMPORTANT]
> Repositori contoh dapat memuat nilai identitas atau kredensial bawaan. Ganti seluruh nilai tersebut
> dan rotasi kredensial apa pun yang pernah tersebar sebelum menjalankan di lingkungan produksi.

---

## Konfigurasi

`config.js` menyimpan pengaturan utama. Kunci yang tidak didefinisikan dilengkapi otomatis oleh
`lib/configDefaults.js` tanpa menimpa nilai yang sudah ada — sehingga pembaruan bot dapat menambahkan
kunci baru (mis. `sewa`, `kipay`) tanpa mengganti config lama.

### Pengaturan utama

| Kunci | Keterangan |
| --- | --- |
| `botName`, `ownerName`, `ownerNumber` | Identitas tampilan dan daftar root owner. **Periksa `ownerNumber`.** |
| `selfMode` | `true` membatasi respons ke owner; `false` membuka akses sesuai plugin. |
| `usePairingCode`, `pairingNumber` | Metode login (kode pairing vs QR) dan nomor pairing. |
| `prefix`, `prefixes`, `multiPrefix` | Awalan perintah. `noPrefix` (lanjutan) untuk perintah tanpa prefix. |
| `sessionName` | Direktori sesi utama (bawaan `session/`). |
| `limit.daily` | Kuota limit harian pengguna biasa. |
| `premium.price` | Harga langganan premium (`perDay`, `perHour`). |
| `jadibot` | Batas & paket harga bot tambahan. |
| `sewa` | Paket sewa grup; `sewa.enforce` membatasi layanan pada grup penyewa. |
| `kipay.api_key` | Kredensial KiPay untuk tagihan QRIS. Gunakan `KIPAY_API_KEY` dari env. Endpoint: `https://api.kipay.biz.id/api/pay` (lihat `lib/payment.js`). |
| `kipay.pollIntervalSec` | Interval poller status transaksi (detik, minimal 3). |
| `replyStyle` | `forward` (kartu dokumen + saluran), `ad` (`externalAdReply`), `plain` (teks). |
| `channel`, `menu.useButtons` | Saluran & tampilan menu (tombol atau teks). |
| `database.path`, `database.autoBackup` | Lokasi database JSON dan salinan cadangan. |
| `enableKeepAliveServer`, `port` | HTTP keep-alive sederhana (`PORT` dapat dioverride env). |
| `webPanelUrl` | Alamat publik panel untuk redirect OAuth & link referral. Bawaan `https://panel.manzxy.biz.id`; bisa diubah dari web (Owner → Alamat panel) atau `WEB_PANEL_URL`. |

### Pengaturan lanjutan

Kunci berikut tersedia lewat `lib/configDefaults.js` dan dapat ditambahkan ke `config.js`:

`register.enforce`, `hotReload`, `botSettings`, `queueConcurrency`, `ignoreOldSeconds`,
`cleaner`, `guard`, `renewal`, `welcome`, `yt`, `timezone`.

Tambahkan **hanya** kunci yang perlu diubah, dengan struktur yang sama seperti default.
Perubahan konfigurasi dibaca saat proses start, sehingga akan diterapkan lewat restart otomatis.

---

## Menjalankan Bot

```bash
npm run pairing   # tautkan dengan kode pairing
npm run qr        # alternatif: tampilkan QR di terminal
npm start         # jalankan kembali memakai sesi tersimpan
```

| Mode | Perilaku |
| --- | --- |
| `npm run pairing` | Memaksa login via kode pairing untuk satu kali jalan (mengimbangi `usePairingCode`). |
| `npm run qr` | Memaksa login via QR terminal untuk satu kali jalan. Terminal harus menampilkan output proses. |
| `npm start` | Memakai sesi tersimpan; jika belum ada, mengikuti `usePairingCode`. |

- Nomor pairing diambil dari `PAIRING_NUMBER` atau ditanyakan melalui terminal.
- Masukkan kode dari menu **Perangkat Tertaut** di WhatsApp.
- Sesi utama disimpan di `sessionName` (bawaan `session/`), sesi jadibot di `sessions_jadibot/`.
- **Jangan jalankan dua proses dengan sesi WhatsApp yang sama.**

### Alur perubahan kode (development)

`npm start`, `npm run pairing`, dan `npm run qr` menjalankan `scripts/dev.js` sebagai supervisor:

- Perubahan pada `config.js`, `handler/`, `lib/`, `plugins/`, dan berkas pendukung memicu restart otomatis.
- Perubahan digabungkan ± 450 ms sebelum restart (debounce).
- Direktori runtime (`session/`, `sessions_jadibot/`, `database/`), `node_modules/`, dan log verifikasi
  dikecualikan agar tidak terjadi restart berulang.
- Jika proses gagal start karena error kode, supervisor tetap hidup dan mencoba lagi pada perubahan berikutnya.
- Supervisor menonaktifkan pemantau plugin internal (agar tidak bekerja ganda).
  - `.reload` → memuat ulang seluruh plugin secara manual.
  - `.hotreload on|off` → mengatur pemantau plugin **hanya** saat menjalankan `node index.js` langsung.

---

## Alur Pesan

```text
index.js
  └─ membuat socket Baileys
handler/events.js
  └─ meneruskan event
handler/parser.js
  └─ membaca teks & prefix
handler/message.js
  ├─ menyaring pesan lama / duplikat
  ├─ menjalankan hook `before`
  ├─ memeriksa hak akses & limit
  └─ mengeksekusi plugin melalui antrean
handler/access.js
  └─ identifikasi pengirim (JID / LID)
```

---

## Fitur dan Perintah

Daftar perintah aktif dibangun dari `plugins/<kategori>/*.js` saat bot start.
Jalankan `.menu` atau `.allmenu` untuk melihat daftar persis di instalasi Anda.
Beberapa perintah mensyaratkan owner, premium, admin grup, atau fitur pembayaran aktif.

| Area | Contoh perintah | Keterangan |
| --- | --- | --- |
| **Umum** | `.menu`, `.profile`, `.daftar`, `.afk` | Menu, profil, pendaftaran, status tidak aktif. |
| **Grup** | `.welcome`, `.antilink`, `.tagall`, `.hidetag`, `.kick`, `.detect` | Sambutan, moderasi, deteksi perubahan. Tindakan tertentu butuh admin bot/pengirim. |
| **Jadibot & Sewa** | `.jadibot`, `.listjadibot`, `.stopjadibot`, `.sewabot`, `.listsewa` | Sesi bot tambahan dan sewa grup. |
| **Premium & Pembayaran** | `.buyprem`, `.perpanjang`, `.batalbayar` | Pembelian, perpanjangan, pengelolaan tagihan. Otomatisasi pembayaran bergantung KiPay. |
| **Alat & Unduhan** | `.calc`, `.qr`, `.s`, `.toimg`, `.ytsearch`, `.play`, `.tiktok`, `.bing` | Pemrosesan lokal dan layanan jaringan. `.s` menerima gambar atau GIF/WebP (sebagai dokumen), bukan MP4. |
| **Permainan** | `.daily`, `.saldo`, `.slot`, `.top` | Koin, klaim harian, peringkat. |
| **RPG** | `.rpg`, `.story`, `.hunt`, `.dailyrpg`, `.bounty`, `.event`, `.profile` | 20 chapter cerita, 8 kelas, 6 region, 31 monster, 12 season, quest harian, kartu profil gambar. |
| **Pengelolaan** | `.setting`, `.self`, `.public`, `.reload`, `.listplug`, `.svplug` | Pengaturan bot dan manajemen plugin. Izin berbeda per perintah. |

### Kategori plugin

`ai` · `downloader` · `game` · `group` · `info` · `interactive` · `jadibot` ·
`main` · `owner` · `plugin` · `premium` · `rpg` · `sewa` · `tools`

### Sistem RPG

Engine modular di `lib/rpg/` (core, items, monsters, systems, story), perintah di
`plugins/rpg/`, data tersimpan di `user.rpg` pada database. Seluruh sistem saling
terhubung: drop monster → material → crafting → enchant → combat lebih kuat →
boss lebih tinggi → season & achievement.

**Modul engine:**

| Berkas | Isi |
| --- | --- |
| `lib/rpg/core.js` | 8 kelas, stat, level, elemen, combat, fuzzy matching |
| `lib/rpg/items.js` | 65 item, 6 rarity, 8 set, resep crafting |
| `lib/rpg/monsters.js` | 6 region, 31 monster, 9 boss, drop table, reward |
| `lib/rpg/systems.js` | Buff, pet, party, guild, craft, enchant, achievement, prestige |
| `lib/rpg/story.js` | Kampanye 20 chapter + 12 season bulanan |
| `lib/rpg/live.js` | Quest harian, streak, combo, world event, bounty, misi mingguan |
| `lib/rpg/card.js` | Render kartu profil gambar (`@napi-rs/canvas`) |
| `lib/rpg/index.js` | API gabungan + `battleReward()` (semua pengali) |

#### Kelas & atribut

| Kelas | Peran | Skill khusus |
| --- | --- | --- |
| ⚔️ Warrior | HP & DEF tertinggi | Cleave |
| 🔮 Mage | Damage sihir terbesar | Meteor |
| 🏹 Archer | Kritikal tinggi | Piercing Shot |
| ✨ Cleric | Self-heal | Holy Heal |
| 🗡️ Rogue | Bonus loot & gold | Backstab |
| ☠️ Necromancer | Damage gelap + drain HP | Soul Siphon |
| 🛡️ Paladin | Tank + heal sendiri | Divine Shield |
| 🪓 Berserker | Damage besar saat HP rendah | Rampage |

Alias Indonesia juga diterima: `ksatria`, `penyihir`, `pemanah`, `pendeta`, `pencuri`.
Nama monster dan wilayah bisa ditulis bebas (`naga merah` → Red Dragon).

- **Stat**: `str` `agi` `vit` `int` `luk` — +5 poin/level plus pertumbuhan kelas.
- **Turunan**: HP, MP, ATK, MATK, DEF, crit rate, crit damage, dodge.
- **Elemen**: Api → Angin → Tanah → Cahaya → Kegelapan → Api (efektif ×1.35, lemah ×0.78).

#### Wilayah & monster

| Region | Level | Monster |
| --- | --- | --- |
| 🌾 Slime Meadow | 1+ | Slime, Giant Rat, Goblin, Wild Boar |
| 🌲 Shadow Forest | 5+ | Shadow Wolf, Giant Spider, Skeleton Warrior, Goblin Chieftain |
| 🏞️ Poison Swamp | 9+ | Bog Zombie, Toxic Toad, Bog Witch |
| 🏛️ Ancient Ruins | 14+ | Brutal Orc, Wraith, Stone Sentinel, Plague Priest |
| 🌋 Volcano Peak | 22+ | Stone Golem, Lava Hound, Fire Elemental, Young Drake |
| 🕳️ Void Abyss | 35+ | Void Walker, Nightmare, Shadow Dragon |

Boss region: Slime King, Forest Guardian, Swamp Hag, Eternal Lich, Red Dragon, Demon Lord.
World boss (raid): Primordial Colossus, Void Dragon, Aetherion.

Monster punya 12% peluang muncul sebagai **Elite** (HP ×1.6, ATK ×1.3, reward ×1.8).
Progres koleksi setiap monster bisa dilihat di `.codex`.

#### Plot kampanye (20 chapter)

Pemain adalah **Yang Terpanggil** — jiwa dari dunia lain yang ditarik ke Aetheria saat
**Segel Purba** mulai retak. Setiap chapter punya lore, dialog, objektif, dan hadiah
yang membuka chapter berikutnya.

Dua belas bulan cerita dibagi dalam empat babak besar:

**Babak I — Kebangkitan (Chapter 1–4)**
1. **The Summoned** — terbangun di dunia asing; suara memanggilmu
2. **The Silent Village** — desa terbakar tanpa mayat; goblin menculik, bukan membunuh
3. **The King Beneath the Mud** — Slime King, jenderal dari perang yang terlupakan
4. **The Whispering Forest** — pohon-pohon menyebut nama *Aetherion*

**Babak II — Harga Sebuah Nama (Chapter 5–8)**
5. **The Guardian's Grief** — penjaga hutan marah karena wilayahnya ditebang
6. **Bones That Stand** — kerangka kuil bangkit karena tugas yang belum selesai
7. **The Poison Court** — Swamp Hag menjual keabadian tanpa nama
8. **The Witch's Price** — harga sebuah kebenaran adalah nama orang yang kau cintai
9. **The Lich That Remembers** — Lich ternyata menahan segel *dari dalam*, 300 tahun
10. **The Mountain That Breathes** — segel pertama sudah retak; golem terbangun

**Babak III — Kebenaran (Chapter 11–15)**
11. **The Wounded Flame** — Red Dragon bukan musuh, hanya terluka oleh jurang
12. **The Abyss Calls** — *"Kau bukan yang pertama, hanya yang terakhir"*
13. **The Sixteen** — enam belas mayat, semuanya berwajahmu
14. **The Throne of Names** — takhta dari nama; namamu sudah terukir sebelum lahir
15. **The Demon Lord** — iblis itu adalah Yang Terpanggil pertama yang menolak pulang

**Babak IV — Akhir Siklus (Chapter 16–20)**
16. **The Seal Broken** — segel hancur, langit terbelah, Aetherion bicara
17. **The Walked Colossus** — penjaga gerbang terakhir
18. **The Void That Remains** — dunia sudah tidak ada di balik gerbang
19. **The Choice** — tutup pintu lalu pulang, atau duduk di takhta
20. **The Eighteenth, No More** — perangi Sang Pencipta Segel; akhiri siklusnya

#### Keseimbangan pertarungan

Kurva stat disetel dan diuji otomatis supaya progresi tetap masuk akal:

- Pemain memakai kurva **super-linear** (`lv^1.25`); monster `lv^1.18`.
- **DEF monster tumbuh lebih lambat** dari HP/ATK (`scale^0.72`) agar damage pemain
  tidak tertelan habis di level tinggi.
- **ATK monster lebih landai** (`scale^0.88`) agar kelas HP-rendah tidak mati
  sebelum sempat membunuh.
- AI skill memilih **heal hanya saat HP < 55%**, jadi tidak ada lagi pertarungan
  yang berputar-putar menyembuhkan diri sampai giliran habis.

Hasil uji (20 pertarungan per kombinasi, poin stat dialokasikan):

| Kelas | Lv1 | Lv10 | Lv25 | Lv50 | Lv80 | Lv120 |
| --- | --- | --- | --- | --- | --- | --- |
| Warrior | 100% t1.4 | 100% t1.0 | 100% t1.4 | 100% t2.4 | 100% t3.9 | 100% t4.7 |
| Mage | 100% t1.4 | 100% t1.0 | 100% t1.0 | 100% t1.3 | 100% t1.9 | 100% t1.9 |
| Archer | 100% t1.9 | 100% t2.6 | 100% t2.9 | 100% t5.5 | 100% t7.3 | 95% t6.6 |
| Cleric | 100% t5.3 | 100% t2.5 | 100% t2.4 | 100% t3.0 | 100% t4.3 | 100% t4.5 |
| Rogue | 100% t2.5 | 100% t2.5 | 100% t2.2 | 100% t4.6 | 100% t5.6 | 95% t5.3 |
| Necromancer | 100% t1.9 | 100% t1.0 | 100% t1.1 | 100% t1.9 | 100% t2.0 | 100% t2.1 |
| Paladin | 100% t5.5 | 100% t3.0 | 100% t2.4 | 100% t2.8 | 100% t3.9 | 100% t4.0 |
| Berserker | 100% t2.3 | 100% t3.6 | 100% t2.5 | 100% t5.5 | 100% t6.8 | 100% t6.7 |

`t` = rata-rata giliran per pertarungan. Kekalahan 5% di level maksimum untuk
kelas HP-rendah disengaja — bukan bug.

> *Aetherion tidak jahat — ia hanya lelah. Ia menciptakan segel dan melelahkannya;
> setiap penjaga harus melupakan siapa dirinya. Kau bisa menjadi penjaga ke-18,
> atau mematahkan siklusnya untuk selamanya.*

Perintah: `.story`, `.story list`, `.story recap`, `.story klaim`.

#### Season bulanan (12 season)

Season berganti setiap bulan dan memberi bonus global + boss + reward eksklusif:

| Bulan | Season | Bonus | Boss |
| --- | --- | --- | --- |
| 1 | 🌅 Dawn of the Summoned | EXP ×1.1 | Slime King |
| 2 | 🔥 Season of Embers | Gold ×1.2 | Red Dragon |
| 3 | 🌸 Bloom of Shadows | Drop ×1.25 | Forest Guardian |
| 4 | 🌫️ The Thin Veil | EXP ×1.2 | Eternal Lich |
| 5 | ⚡ Silver Storm | Gold ×1.3 | Demon Lord |
| 6 | ☀️ The Longest Day | EXP ×1.3 | Primordial Colossus |
| 7 | 🕳️ The Hollow | Drop ×1.3 | Demon Lord |
| 8 | 🌾 Blood Harvest | Gold ×1.4 | Red Dragon |
| 9 | 🌑 Eternal Eclipse | EXP ×1.4 | Demon Lord |
| 10 | 🕯️ Requiem | EXP & Gold ×1.25 | Eternal Lich |
| 11 | ❄️ Frostfall | Drop ×1.35 | Swamp Hag |
| 12 | ✨ The Eternal Night | EXP/Gold/Drop ×1.5 | Void Dragon |

Setiap season punya **3 tier reward** yang bisa diklaim bertahap, tetapi hanya
setelah mengalahkan boss season tersebut. Progres disimpan di `data.seasons[YYYY-MM]`
sehingga reward tidak bisa diklaim dua kali. Lihat kalender lengkap: `.season list`.

#### Sistem pendukung yang saling terhubung

| Sistem | Perintah | Bergantung pada |
| --- | --- | --- |
| **Party** | `.party buat/join/keluar` | 5 anggota, bonus EXP ×1.15 & gold ×1.10 per orang |
| **Guild** | `.guild buat/join/bank` | 5.000 gold, bonus EXP/gold + stat per level |
| **Pet** | `.pet beli/beri/lepas` | gold, naik level dari perburuan, bonus stat |
| **Crafting** | `.craft`, `.craft <item>` | material dari drop monster |
| **Enchant** | `.enchant <slot>`, `.dismantle <item>` | debu dari bongkar item |
| **Buff** | `.use atk_potion` dll | potion, berlaku 10–15 menit |
| **Achievement** | `.achieve` | 12 pencapaian → title → bonus permanen |
| **Title** | `.title <nama>` | dibuka lewat achievement |
| **Prestige** | `.prestige` | level 50, reset progres untuk +5 LUK |
| **Raid** | `.raid` | party menyumbang damage ke world boss |
| **Turnamen** | `.tournament` | bandingkan power dengan petualang lain |

#### Fitur harian & retensi

Sistem yang membuat pemain kembali tiap hari — semuanya saling terhubung dan
otomatis tercatat dari aktivitas berburu:

| Fitur | Perintah | Cara kerja |
| --- | --- | --- |
| **Quest Harian** | `.dailyrpg` | 4 quest acak per hari per pemain (deterministik dari tanggal + nomor). Selesaikan semua → bonus 🪙5.000 + 🔮100 |
| **Login Streak** | otomatis | Bonus gold/EXP naik sampai hari ke-7; milestone hari 3/7/14/30 memberi item langka |
| **Combo** | otomatis | Berburu berturut dalam 90 detik: +8% reward per combo, maksimal **10x (+72%)**. Kalah = combo putus |
| **World Event** | `.event` | Berotasi tiap **4 jam** dan berlaku untuk semua pemain: Bulan Darah, Lonjakan Mana, Hujan Harta, Gerhana, Demam Berburu |
| **Bounty Harian** | `.bounty` | Satu boss jadi target buronan; hadiah besar, targetnya sama untuk semua pemain |
| **Misi Mingguan** | `.dailyrpg mingguan` | Target 150 kill / 10 boss / 5 dungeon / 100k gold, reset tiap Senin |
| **Peringkat baru** | `.rank streak` `.rank combo` `.rank chapter` `.rank achieve` | Daya saing dari konsistensi, bukan cuma level |

Semua pengali ini **bertumpuk** di `battleReward()`:

```
reward akhir = dasar × party × guild × season × buff × combo × world event
```

#### Kartu profil bergambar

`.profile` mengirim **kartu gambar 1000×620** (gaya NEO BRUTALISM) berisi:

- Avatar WhatsApp + lencana level · nama · kelas · title · prestige
- Bar EXP / HP / MP, serta ATK · MATK · DEF · POWER · CRIT
- Panel progres: chapter, achievement, kill, boss, dungeon, quest harian
- Panel harta: gold, dust, jumlah item
- Panel streak 🔥 dan combo ⚡
- Footer: pet, guild, peringkat, set aktif

Butuh `@napi-rs/canvas` (sudah ada di `dependencies`). Bila modul itu tidak
tersedia atau pengambilan avatar gagal, perintah otomatis jatuh ke versi teks —
tidak pernah error ke pengguna. Paksa mode teks dengan `.profile text`.

#### Daftar perintah RPG

**Karakter** `.rpg` / `.char` — status lengkap · `.rpg daftar <kelas>` · `.rpg alloc <stat> <n>`
**Cerita** `.story` · `.story list` · `.story recap` · `.story klaim`
**Season** `.season` · `.season list` · `.season claim`
**Pertarungan** `.hunt [monster|region]` · `.boss [nama]` · `.dungeon` · `.raid` · `.tournament`
**Dunia** `.region` · `.monsters [boss|raid]` · `.codex [region]`
**Sosial** `.party buat/join/keluar` · `.guild buat/join/bank/keluar`
**Item** `.inv` · `.use` · `.equip` · `.unequip` · `.shop` · `.buy` · `.sell` · `.craft`
**Peningkatan** `.enchant <slot>` · `.dismantle <item>` · `.skill` · `.heal`
**Harian** `.dailyrpg` · `.dailyrpg klaim <n|all>` · `.dailyrpg mingguan` · `.bounty` · `.event`
**Progres** `.achieve` · `.title` · `.prestige` · `.rank [mode]`
**Profil** `.profile` (kartu gambar) · `.profile text`

Contoh: `.hunt naga merah` akan mencari **Red Dragon**, bukan jatuh ke default.

Plugin `amverify` menggunakan layanan pihak ketiga dan menulis log lokal — baca bagian
[Keamanan](#keamanan-dan-penanganan-data) sebelum menggunakannya.
Fitur status WhatsApp dan saluran ditangani handler (lihat `statusCommand`, `channelCommand`).

---

## Susunan Proyek

```text
ManzxyBase/
├── index.js                 # koneksi WhatsApp, login, reconnect, lifecycle bot
├── config.js                # pengaturan instalasi
├── ecosystem.config.js      # konfigurasi proses PM2
├── scripts/
│   └── dev.js               # supervisor perubahan file & siklus proses
├── handler/
│   ├── index.js             # entry handler
│   ├── events.js            # penerus event socket
│   ├── parser.js            # parsing teks & prefix
│   ├── message.js           # filter, hook, akses, antrean eksekusi
│   ├── access.js            # identifikasi pengirim (JID/LID)
│   ├── antiCall.js          # penanganan panggilan
│   ├── groupDetect.js       # deteksi perubahan grup
│   ├── poll.js              # polling
│   └── texts.js             # teks balasan
├── lib/
│   ├── database.js          # JSON DB tertunda + backup
│   ├── pluginLoader.js      # pemuat plugin
│   ├── pluginManager.js     # manajemen plugin runtime
│   ├── projectWatcher.js    # pemantau direktori proyek
│   ├── jadibotManager.js    # pengelolaan sesi jadibot
│   ├── payment.js           # integrasi KiPay/QRIS
│   ├── rental.js            # sewa grup
│   ├── renewal.js           # perpanjangan & pengingat
│   ├── economy.js           # koin, limit, saldo
│   ├── promotions.js        # promo & diskon
│   ├── rpg.js               # shim kompatibilitas + re-export engine RPG
│   ├── rpg/                 # engine RPG modular
│   │   ├── core.js          # stat, level, elemen, combat, profil
│   │   ├── items.js         # item, rarity, set bonus, resep crafting
│   │   ├── monsters.js      # region, monster, drop table, reward
│   │   ├── systems.js       # buff, pet, party, guild, craft, enchant, achievement
│   │   ├── story.js         # kampanye 20 chapter + 12 season
│   │   ├── live.js          # quest harian, streak, combo, world event, bounty
│   │   ├── card.js          # kartu profil gambar (canvas)
│   │   └── index.js         # API gabungan + battleReward()
│   ├── richHelper.js        # helper UI kaya (tombol/carousel/ad)
│   ├── sysinfo.js           # spesifikasi server + riwayat metrik + snapshot publik
│   ├── keepAlive.js         # server HTTP keep-alive
│   ├── web/                 # panel web (lihat "Panel Web")
│   │   ├── server.js        # REST API + routing + static server
│   │   ├── auth.js          # email/password (scrypt) + OAuth Google/GitHub
│   │   ├── store.js         # SQLite lokal: akun, sesi, oauth, log
│   │   └── public/          # index.html, style.css, app.js (neobrutalism)
│   ├── sticker.js           # pembuatan stiker (sharp)
│   └── ...                  # helper lainnya (cache, logger, utils, dll.)
├── plugins/<kategori>/*.js  # perintah & hook per fitur
├── assets/                  # berkas pendukung fitur
├── tests/                   # pengujian otomatis (node:test)
├── session/                 # [runtime] kredensial bot utama
├── sessions_jadibot/        # [runtime] kredensial jadibot
└── database/                # [runtime] data JSON & backup + webpanel.db (SQLite)
```

> [!NOTE]
> `session/`, `sessions_jadibot/`, `database/`, dan `verification-log.txt` adalah **data runtime**.
> Jangan menghapus atau menimpanya saat merapikan struktur source.

`lib/database.js` menulis `database/db.json` secara tertunda dan membuat `.bak` bila backup diaktifkan.
Data mencakup pengguna, grup, sesi jadibot, transaksi, pengingat, dan statistik.

---

## Panel Web (Jadibot)

Panel berbasis browser dengan tema **neobrutalism**, berjalan di **port yang sama** dengan
keep-alive sehingga tidak perlu membuka port tambahan.

```text
https://panel.manzxy.biz.id/panel
```

Alamat publik panel diatur lewat **Owner → Alamat panel** (atau `WEB_PANEL_URL`). Panel juga bisa
dibuka dari host mana pun — `/`, `/login`, `/dashboard`, dan `/profile` otomatis dialihkan ke `/panel`.

Port lokal mengikuti `config.port` (bawaan `3000`, atau `process.env.PORT`).

### Database lokal

Akun panel disimpan **terpisah** dari `database/db.json` milik bot, di:

```text
database/webpanel.db          # SQLite (node:sqlite, bawaan Node 22+)
```

Tabel: `accounts`, `oauth`, `sessions`, `logs`, `settings`.
Data bot (saldo, order, jadibot) tetap dibaca langsung dari `database/db.json`
supaya angka di panel selalu sama dengan yang dilihat bot.

> [!IMPORTANT]
> Panel web memerlukan **Node.js 22+** karena memakai modul bawaan `node:sqlite`.
> Tidak ada dependency tambahan yang perlu diinstal.

### Cara daftar & login

Tiga jalur, semuanya dari halaman `/panel`:

| Metode | Cara |
|--------|------|
| **Email + password** | Tab **DAFTAR** — isi email, nama, nomor WA (**wajib**, diverifikasi OTP WhatsApp), dan password |
| **Google** | Klik tombol **Google** (OAuth 2.0) — aktif setelah owner mengisi kredensial |
| **GitHub** | Klik tombol **GitHub** (OAuth 2.0) — aktif setelah owner mengisi kredensial |

Password minimal 8 karakter dan harus memuat huruf + angka.
Akun Google/GitHub yang emailnya sama akan otomatis **ditautkan ke akun yang sudah ada**
(tidak membuat duplikat). Satu perangkat hanya boleh memiliki satu akun (dikunci lewat device hash)
agar bonus referral tidak bisa disalahgunakan.

Keamanan login tambahan: **2FA TOTP** (Google Authenticator/Authy) bisa diaktifkan di tab Profil,
dan login dibatasi rate limit + lockout 15 menit setelah 5 percobaan gagal.

### Menghubungkan nomor WhatsApp

Nomor WA adalah jembatan antara akun web dan data bot. Isi saat mendaftar,
atau kapan saja di tab **Profil → Nomor WhatsApp**.

Tanpa nomor WA, akun tetap bisa login dan melihat profil, tetapi
**jadibot, saldo, topup, dan transaksi tidak akan muncul**.

### Konfigurasi OAuth (dari panel web — tanpa VPS)

Login **Google** dan **GitHub** diatur langsung dari browser, tidak perlu mengedit `.env`
maupun restart bot. Tombol provider langsung aktif begitu kredensial disimpan.

**Langkah:**

1. Login ke panel sebagai akun **owner** (role owner didapat dari nomor WhatsApp root owner).
2. Buka tab **👑 OWNER → 🔐 LOGIN GOOGLE / GITHUB (DIATUR DARI WEB)**.
3. Periksa **Alamat panel (base URL)** — isinya hanya domain, contoh:
   `https://panel.manzxy.biz.id` (tanpa `/panel`, tanpa garis miring di akhir).
4. Isi **Client ID** + **Client Secret** milik provider, lalu klik **SIMPAN**.
5. Salin **Redirect URI** yang tertulis di kartu tersebut dan daftarkan di provider.

| Provider | Halaman pembuatan | Redirect URI yang didaftarkan |
|---|---|---|
| **Google** | <https://console.cloud.google.com/apis/credentials> → *Create credentials → OAuth client ID → Web application* | `https://panel.manzxy.biz.id/api/auth/google/callback` |
| **GitHub** | <https://github.com/settings/developers> → *New OAuth App* | `https://panel.manzxy.biz.id/api/auth/github/callback` |

Ganti `panel.manzxy.biz.id` dengan domain panel Anda jika berbeda — nilainya selalu mengikuti
**Alamat panel (base URL)** di halaman Owner, dan panel menampilkan URI yang tepat.

> [!NOTE]
> Client **Secret** disimpan di `database/webpanel.db` (tabel `settings`) dan **tidak pernah**
> dikirim kembali ke browser — kolom secret hanya menampilkan status "tersimpan".
> Client **ID** boleh terlihat karena memang tampil di URL OAuth publik.

#### Urutan prioritas alamat panel

Nilai base URL selalu dinormalkan (spasi, garis miring akhir, dan path seperti `/panel` dibuang),
sehingga redirect tidak pernah menjadi `https://panel.manzxy.biz.id/panel/panel`. Sumbernya, dari
yang paling diutamakan:

1. Nilai yang disimpan owner dari panel (**Alamat panel**).
2. `WEB_PANEL_URL` di environment.
3. `webPanelUrl` di `config.js` (bawaan `https://panel.manzxy.biz.id`).
4. Tebakan dari header request (hanya bila ketiganya kosong).

Bila nilai tersimpan masih menunjuk host lokal (`localhost`, `127.0.0.1`, IP privat) sedangkan
env/config sudah berisi domain publik, panel memakai domain publik supaya tidak salah redirect
saat uji di VPS.

Kalau kredensial belum diisi, tombol provider otomatis nonaktif dan form email/password tetap
bisa dipakai. Konfigurasi lewat environment (`GOOGLE_CLIENT_ID`, `GOOGLE_CLIENT_SECRET`,
`GITHUB_CLIENT_ID`, `GITHUB_CLIENT_SECRET`) masih didukung untuk deployment otomatis — nilai dari
panel menang bila keduanya terisi.

### Halaman

| Tab | Isi |
|-----|-----|
| **Dashboard** | Jadibot online/pairing, slot server, uptime, pesan & command, info akun, peringatan bila WA belum tertaut |
| **Monitor** | Kartu semua jadibot: status, mode self/public, uptime, masa aktif, owner; tombol MATIKAN / NYALAKAN / PUBLIC-SELF / HAPUS |
| **Jadibot** | Beli jadibot memakai saldo, tabel harga + diskon, panduan kode pairing |
| **Topup Saldo** | 5 paket saldo, QR QRIS dirender di panel, status dicek otomatis tiap 4 detik |
| **Transaksi** | Riwayat semua order dengan badge status |
| **Profil** | Avatar, nama, nomor WA, ganti password, kelola akun tertaut (Google/GitHub), log aktivitas, hapus akun |
| **👑 Owner** | Slot server, paket durasi, diskon & promo, statistik, kelola pengguna bot, akun panel, log, **serta pengaturan Login Google/GitHub & alamat panel** |
| **💻 Sistem** | Telemetri server khusus owner: spek CPU/RAM/disk/suhu, grafik riwayat beban, dan trafik pesan & command per jam. Lihat [Telemetri Server](#telemetri-server). |
| **🗄️ Database** | Penjelajah `database/db.json` (khusus owner): daftar koleksi, pencarian, pagination, JSON mentah |

### Saldo dan topup

- Topup membuat order KiPay bertipe `topup`; saldo masuk **setelah** status `paid` terverifikasi.
- Saldo (`user.money` di `db.json`) bisa dipakai membeli jadibot langsung dari panel.
- Jika pembuatan jadibot gagal, saldo otomatis dikembalikan (tidak hangus).
- Paket dan bonus diatur di `lib/web/server.js` fungsi `topupPackages()`.

### Endpoint API

| Method | Path | Auth | Keterangan |
|--------|------|------|------------|
| GET | `/api/health` | publik | Status server |
| GET | `/api/auth/providers` | publik | Provider OAuth yang aktif |
| POST | `/api/auth/register` | publik | Daftar email + password |
| POST | `/api/auth/login` | publik | Login email + password |
| POST | `/api/auth/logout` | publik | Hapus sesi |
| GET | `/api/auth/:provider/start` | publik | Mulai OAuth (redirect ke provider) |
| GET | `/api/auth/:provider/callback` | publik | Callback OAuth |
| GET | `/api/admin/oauth` | owner | Status kredensial Google/GitHub + redirect URI + alamat panel |
| POST | `/api/admin/oauth` | owner | Simpan alamat panel & kredensial OAuth (`baseUrl`, `google`, `github`) |
| GET | `/api/me` | token | Profil, saldo, jadibot, order, log |
| POST | `/api/profile` | token | Ubah nama / nomor WA / avatar |
| POST | `/api/profile/password` | token | Ganti / buat password |
| POST | `/api/profile/unlink` | token | Lepas tautan Google/GitHub |
| POST | `/api/profile/delete` | token | Hapus akun (`confirm: "HAPUS"`) |
| GET | `/api/monitor` | token | Snapshot jadibot + ringkasan |
| GET | `/api/admin/sysinfo` | owner | Spesifikasi server (CPU, RAM, disk, suhu, runtime). Non-pribadi. |
| GET | `/api/admin/sysinfo/history` | owner | Deret metrik beban untuk grafik (sampel 15 detik, ~1 jam) |
| GET | `/api/admin/sysinfo/traffic` | owner | Trafik pesan & command per jam (`?hours=1…96`, bawaan 24) |
| POST | `/api/jadibot/start` | token | Nyalakan / buat jadibot |
| POST | `/api/jadibot/stop` | token | Matikan (`deleteSession: true` = hapus sesi) |
| POST | `/api/jadibot/mode` | token | Ubah mode self/public |
| POST | `/api/jadibot/buy` | token | Beli jadibot dengan saldo |
| GET | `/api/topup/packages` | token | Daftar paket saldo |
| POST | `/api/topup/create` | token | Buat tagihan QRIS |
| GET | `/api/topup/status` | token | Status tagihan |
| GET | `/api/qr` | token | QR PNG sebagai data URL |
| GET | `/api/orders` | token | Riwayat transaksi |

Autentikasi memakai cookie sesi (`mz_session`, HttpOnly) atau header
`Authorization: Bearer <token>`. Sesi berlaku 12 jam dan disimpan di SQLite.

### Keamanan

- Password di-hash dengan **scrypt** (salt acak 16 byte per akun).
- OAuth memakai `state` acak berumur 10 menit, sekali pakai, dan terikat ke provider.
- Hash & salt password tidak pernah dikirim ke klien (`publicAccount()` membuangnya).
- User hanya bisa melihat dan mengubah jadibot miliknya sendiri; Root Owner melihat semuanya.
- Menghapus akun mencabut semua sesi dan menghapus data terkait (cascade).

> [!TIP]
> Alamat panel (`WEB_PANEL_URL`) dipakai untuk redirect OAuth dan tautan undangan referral.
> Ubah kapan saja dari tab **👑 OWNER → Alamat panel** — tidak perlu restart bot, dan nilainya
> otomatis dibersihkan dari path `/panel`.

---

## Telemetri Server

Halaman **SISTEM** di panel web (`/panel` → tab 💻) menampilkan kondisi mesin tempat bot berjalan,
dan perintah `.sysinfo` menampilkan ringkasan yang sama langsung di chat.

Keduanya membaca sumber data yang sama: `lib/sysinfo.js`. Angka di chat dan di panel karena itu
tidak pernah berbeda.

```text
   WhatsApp  ──.sysinfo──┐
                         ├──▶  lib/sysinfo.js  ──▶  snapshot()         → spek + metrik
   Panel Web ──/api──────┘                     └──▶  publicSnapshot()   → spek tanpa data pribadi
                                                └──▶  historySeries()   → deret untuk grafik
```

### Data yang dikumpulkan

| Metrik | Sumber | Keterangan |
| --- | --- | --- |
| Model CPU | `lscpu` → `/proc/cpuinfo` → DMI | Nama prosesor sebenarnya, termasuk di VM/Proxmox |
| Jumlah core & frekuensi | `os.cpus()` | vCPU yang dialokasikan |
| Load 1/5/15 menit | `os.loadavg()` | Dibandingkan dengan jumlah core untuk persentase |
| Suhu CPU | `/sys/class/thermal/thermal_zone0/temp` | Kosong bila sensor tidak tersedia |
| RAM total/terpakai | `os.totalmem()` / `os.freemem()` | Termasuk RSS & heap proses bot |
| Kapasitas disk | `df -Pk` | Per mount point, diurutkan |
| Nama OS & kernel | `/etc/os-release`, `os.release()` | Fallback Android untuk Termux |
| Lingkungan | `/.dockerenv`, `/proc/1/cgroup`, `TERMUX_VERSION` | Bare-metal / container / Termux |
| Jumlah port | `ss -ltnp` → `netstat` | **Hanya jumlahnya**, daftar port tidak dikirim ke web |

Semua pembacaan dibungkus `try/catch` — bila satu sensor tidak tersedia (misalnya di container
tanpa `/sys`), metrik itu bernilai kosong dan metrik lain tetap tampil.

### Grafik dan riwayat

Metrik berat di-sampling tiap **15 detik** dan disimpan di memori sebanyak **240 titik**
(≈ 1 jam). Tidak ada berkas tambahan yang ditulis ke disk; riwayat hilang saat proses restart
dan terisi kembali secara otomatis.

Halaman SISTEM menyediakan:

- **Empat meter** — CPU load, RAM, disk, dan suhu, dengan warna adaptif (hijau → kuning → merah).
- **Empat sparkline** — CPU, RAM, RAM bot (RSS), dan suhu, masing-masing dengan nilai
  sekarang / minimum / maksimum / rata-rata.
- **Pemilih rentang** — 15 menit, 1 jam, 4 jam, atau seluruh riwayat.
- **Trafik per jam** — diagram batang pesan dan command, dapat dipilih 24 / 48 / 96 jam.
- **Auto-refresh 10 detik** — opsional, otomatis berhenti ketika tab ditinggalkan.

Riwayat trafik dicatat di `stats.traffic` pada `database/db.json` dengan bentuk
`{ "YYYY-MM-DD HH": { "messages": n, "commands": n } }`, disimpan maksimal 96 jam terakhir.

### Batas privasi

Endpoint web memakai `publicSnapshot()`, yang **membuang secara sengaja**:

| Dibuang | Alasan |
| --- | --- |
| Nama host dan alamat IP | Identitas serta topologi jaringan |
| PID dan direktori kerja | Struktur berkas server |
| Nama perangkat disk | Jejak penyimpanan fisik |
| Daftar port (hanya jumlah) | Dapat membocorkan layanan yang berjalan |
| Nomor, email, nama pengguna, isi grup | Data pribadi |
| Statistik transaksi dan jadibot | Data pribadi |

Trafik hanya berisi **jumlah**, tanpa pengirim, nama grup, atau isi pesan. Seluruh endpoint
telemetri berada di bawah `requireOwner` dan mengembalikan `401` untuk permintaan tanpa sesi.

### Perintah `.sysinfo`

| Perintah | Keterangan |
| --- | --- |
| `.sysinfo` | Spek lengkap: host, CPU, RAM, disk, jaringan, runtime, dan data bot |
| `.sysinfo brief` | Hanya ringkasan angka penting |
| `.spek`, `.server`, `.ram` | Alias perintah yang sama |

Keluaran chat menyertakan alamat IP lokal dan daftar port karena hanya terkirim ke ruang
percakapan owner. Versi web tidak memuat keduanya.

---

## Mengembangkan Plugin

Simpan plugin CommonJS di `plugins/<kategori>/<nama>.js`.
Pemuat plugin menerima `command` (string/array) + `execute()`, atau plugin tanpa perintah
dengan hook `before()`, `all()`, maupun `events`.

```js
module.exports = {
  name: 'Contoh',
  command: ['contoh'],
  category: 'tools',
  description: 'Membalas teks yang dikirim pengguna',
  limit: 0,
  async execute(sock, m, { text, reply }) {
    await reply(`Teks: ${text}`);
  }
};
```

### Properti akses

`rootOwner`, `owner`, `premium`, `group`, `private`, `admin`, `botAdmin`, `register`

- Biaya limit diambil dari properti `limit` plugin atau aturan kategori
  (`limit.freeCategories` bebas biaya).
- `execute()` yang mengembalikan `false` **tidak** mengurangi limit.
- Hook `before(m, ctx)` berjalan sebelum pencarian perintah; return `true` untuk menghentikan pemrosesan.
- Daftarkan penangan event socket pada properti `events`.
- Gunakan `reply` dari konteks atau helper `sock` di `lib/richHelper.js` untuk respons kaya.

### Reload plugin

| Skenario | Perilaku |
| --- | --- |
| `npm start` / PM2 (`scripts/dev.js`) | Perubahan plugin memicu **restart penuh** — perubahan di `lib/` & `handler/` ikut terbaca. |
| `node index.js` + `config.hotReload` | Pemuat plugin hanya memantau file `.js` di folder kategori. |
| Kapan saja | `.reload` memuat ulang **seluruh** plugin secara manual. |

---

## Pengujian

```bash
npm run check   # periksa sintaks entry point, handler, dan supervisor
npm test        # jalankan unit test (node:test)
```

Cakupan saat ini:

- `tests/promotions.test.js` — logika promo & diskon.
- `tests/projectWatcher.test.js` — pemantauan proyek & debounce.
- `tests/rpg.test.js` — engine RPG: stat, item, set bonus, combat, kampanye, season, pet, craft, enchant, achievement, prestige.

> [!NOTE]
> `npm run check` **bukan** pemeriksaan sintaks untuk seluruh plugin, dan unit test **tidak**
> membuktikan koneksi WhatsApp maupun transaksi pembayaran nyata. Uji fitur yang terdampak
> secara terpisah sebelum dipakai di akun aktif.

---

## Deployment di Server

`ecosystem.config.js` menjalankan satu supervisor `scripts/dev.js` melalui PM2 dengan
`watch: false` (agar tidak ada dua pemantau yang bertabrakan).

```bash
npm ci
# sesuaikan config.js, lalu:
pm2 start ecosystem.config.js
pm2 save
pm2 logs ManzxyMD
```

Catatan operasional:

- **Satu supervisor untuk satu sesi WhatsApp.** Jangan menyalakan `npm start` dan PM2 bersamaan.
- Setelah `npm install`, lakukan restart terencana (`pm2 restart ManzxyMD`) karena `node_modules/` tidak dipantau.
- HTTP keep-alive menyajikan JSON pada `/` dan `/health` bila diaktifkan. Respons itu **hanya** menandakan
  proses HTTP hidup — bukan jaminan koneksi WhatsApp atau kesiapan pembayaran.
- Batasi akses port tersebut lewat firewall/reverse proxy sesuai kebutuhan deployment.

---

## Keamanan dan Penanganan Data

- **Data sensitif** — `session/`, `sessions_jadibot/`, `database/`, `verification-log.txt`,
  `.env`, dan arsip backup. Jangan unggah ke repositori atau bagikan via chat publik.
  Atur permission file & cadangan sesuai lingkungan server.
- **Konfigurasi** — periksa `config.js` sebelum distribusi; ganti/nonaktifkan identitas & kredensial
  bawaan dan rotasi kredensial yang pernah tersebar. Jangan menempelkan API key ke README, issue, atau log.
- **Root owner = akses penuh** — plugin root owner dapat menjalankan JavaScript/shell (`.eval`, `.exec`),
  mengubah file (`.svfl`, `.edfl`), dan memasang plugin dari chat. Berikan hanya kepada nomor yang Anda kendalikan
  dan tinjau kode plugin sebelum dipasang.
- **Backup** — `.backupsc` bukan jaminan cadangan yang aman untuk dibagikan; pengecualiannya terbatas.
  Gunakan cadangan terenkripsi di luar mesin bila data perlu dipulihkan.
- **Verifikasi pihak ketiga** — plugin verifikasi dapat mencatat email, cookie, tautan verifikasi, dan token
  ke `verification-log.txt`. Batasi akses, tetapkan kebijakan retensi, dan gunakan hanya dengan izin pemilik akun.
- **Pihak ketiga** — koneksi layanan luar dan pembayaran melibatkan layanan eksternal. Uji di lingkungan
  yang sesuai sebelum dipakai publik.

---

## Troubleshooting

| Gejala | Penyebab umum & solusi |
| --- | --- |
| **Bot diam** | Periksa `selfMode`, prefix, izin plugin, dan log proses. Di grup, pastikan bot memiliki hak admin bila perintah memerlukannya. |
| **Pairing / sesi gagal** | Pastikan hanya satu proses memakai sesi tersebut; periksa status penautan di WhatsApp. Logout dapat menghapus sesi utama untuk meminta login ulang. |
| **Stiker gagal dibuat** | Pastikan `sharp` terpasang. Video MP4 tidak didukung plugin stiker ini. |
| **Pembayaran tidak tersedia** | Periksa kredensial & konektivitas KiPay — **tanpa** mencetak API key ke log. |
| **Perubahan `lib/` / `config.js` belum terasa** | Pastikan bot dijalankan via `npm start` atau PM2, bukan `node index.js` langsung. Pemantau plugin bawaan hanya memuat ulang plugin. |
| **Restart berulang** | Periksa error sintaks di file yang baru diubah; supervisor akan terus mencoba. Lihat log proses untuk stack trace. |
| **Panel error setelah update** | Browser masih memakai HTML lama. Lakukan hard refresh (Ctrl+Shift+R). Server sudah mengirim `Cache-Control: no-store` dan versi aset otomatis (`app.js?v=…`), sehingga ini hanya perlu sekali. |
| **Grafik SISTEM kosong** | Riwayat disimpan di memori dan dimulai dari nol setiap restart. Biarkan panel terbuka — grafik terisi tiap 15 detik. |
| **Suhu CPU tidak tampil** | Sensor tidak tersedia (umum pada container atau VM tertentu). Metrik lain tetap berjalan. |

---

## Kontribusi

1. Buat branch fitur: `git checkout -b feat/nama-fitur`.
2. Ikuti struktur plugin yang ada dan gaya CommonJS.
3. Jalankan `npm run check` dan `npm test` sebelum commit.
4. Pastikan **tidak** ada berkas runtime/kredensial yang ikut ter-commit (lihat `.gitignore`).
5. Ajukan pull request dengan deskripsi perubahan dan dampaknya.

---

## Lisensi dan Atribusi

Proyek ini tidak menyertakan berkas lisensi eksplisit; hak penggunaan mengikuti kebijakan pemilik repositori.
Dependensi pihak ketiga memiliki lisensinya masing-masing.

- [Baileys](https://github.com/WhiskeySockets/Baileys) — pustaka WhatsApp Web API.
- [Node.js](https://nodejs.org/), [sharp](https://sharp.pixelplumbing.com/), [@napi-rs/canvas](https://github.com/Brooooooklyn/canvas).

<div align="center">
<sub>ManzxyMD v8.11.1 — dibuat untuk pengelolaan akun & grup milik sendiri.</sub>
</div>
