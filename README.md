# Sistem Pengurusan Kelas Mengaji Syafie Legacy (SPKM)

SPKM ialah portal pengurusan kelas mengaji untuk pendaftaran murid, kehadiran, yuran, carian rekod, pengurusan guru dan operasi pentadbiran.

## Status Semasa

Setakat 16 Ogos 2026:

- PWA awam beroperasi seperti biasa dan telah disahkan secara visual.
- eBayar legacy kekal digunakan untuk Januari hingga Ogos 2026.
- Native eBayar disediakan untuk September 2026 dan bulan seterusnya, tertakluk kepada bulan semasa dan Portal Mode.
- Portal Mode semasa ialah `AUTO`: legacy sebelum 1 September 2026 dan Native mulai tarikh tersebut.
- Migrasi serta validasi shadow eBayar V2 bagi Januari hingga Ogos 2026 telah selesai.
- Kod Native eBayar Phase 2A dan Phase 2B telah disiapkan serta dihantar ke editor/source Apps Script, tetapi belum diuji dengan transaksi sebenar. Existing active Web App production belum ditetapkan kepada versi baharu yang mengandungi Phase 2A/2B.

Rujuk [CURRENT_STATUS.md](CURRENT_STATUS.md) sebelum memulakan kerja atau deployment seterusnya.

## Modul Utama

- Pendaftaran murid kanak-kanak dan dewasa dengan pengesahan OTP.
- Kehadiran guru, termasuk sokongan guru backup/relief.
- Dashboard yuran, sejarah bayaran dan eSemak.
- Legacy eBayar melalui Google Form bagi tempoh sejarah.
- Native eBayar berbilang murid dengan semakan kelayakan, slip bank dan resit PDF.
- Pengurusan murid, guru, pertukaran guru dan WhatsApp blast.
- PWA untuk akses mudah alih.

## Struktur Projek

- `Code.js` — backend Google Apps Script.
- `portal.html` — antaramuka portal GAS.
- `index.html` — PWA awam di GitHub Pages.
- `TestWA.js` — helper berkaitan WhatsApp.
- `appsscript.json` — konfigurasi Apps Script.
- `sw.js` dan `manifest.json` — service worker dan manifest PWA.
- `CURRENT_STATUS.md` — checkpoint operasi semasa dan langkah sesi seterusnya.
- `REFERENCE.md` — rujukan teknikal dan deployment.
- `INTERNAL_OPERATIONS.md` — runbook operasi dalaman.
- `SPKM_V2_PLAN.md` — pelan asal dan rekod milestone V2.
- `CHANGELOG.md` — sejarah perubahan.

## Deployment

Repositori menggunakan dua remote dengan tujuan berbeza:

- `origin` — repositori pembangunan/sumber.
- `pages` — repositori production GitHub Pages.

Push ke `origin` tidak mengemas kini laman Pages. Sebelum push ke `pages`, fetch kedua-dua remote dan semak divergence. Untuk Apps Script, `clasp push` hanya mengemas kini editor/source. Production behavior hanya berubah apabila existing active Web App deployment diedit dan ditetapkan kepada `New version`; URL deployment production yang sama perlu dikekalkan. Jangan cipta deployment baharu kecuali memang dimaksudkan.

## Privasi dan Keselamatan

- Jangan masukkan ID murid berasaskan MyKid/MyKad ke browser atau dokumentasi awam.
- Jangan masukkan credential, token, data peribadi atau pautan fail private dalam commit.
- Jangan bypass sempadan bulan, duplicate guard, lock atau semakan pasca-write untuk ujian mudah.
- Jangan jalankan semula bayaran apabila keputusan write tidak pasti; semak staging terlebih dahulu.

## Dokumentasi

- Status semasa: [CURRENT_STATUS.md](CURRENT_STATUS.md)
- Operasi dalaman: [INTERNAL_OPERATIONS.md](INTERNAL_OPERATIONS.md)
- Rujukan teknikal: [REFERENCE.md](REFERENCE.md)
- Pelan V2: [SPKM_V2_PLAN.md](SPKM_V2_PLAN.md)
- Sejarah perubahan: [CHANGELOG.md](CHANGELOG.md)
