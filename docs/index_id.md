## PERINGATAN

Proyek ini tidak bertanggung jawab atas: perangkat yang rusak, kartu SD yang rusak, atau SoC yang terbakar.

**Harap pastikan file konfigurasi Anda tidak menyebabkan loop lalu lintas, jika tidak maka dapat menyebabkan ponsel Anda restart tanpa batas.**

Jika Anda benar-benar tidak tahu cara mengonfigurasi modul ini, Anda mungkin memerlukan aplikasi seperti **ClashForAndroid, ClashMetaForAndroid, v2rayNG, Surfboard, SagerNet, AnXray, NekoBox**, dll.

## Install

- Unduh paket zip modul dari RELEASE dan instal melalui `Magisk` / `KernelSU`. Saat menginstal, Anda akan ditanya apakah akan mengunduh paket lengkap, Anda dapat memilih **unduhan lengkap** atau **unduhan terpisah** nanti, lalu mulai ulang perangkat.
- Mod ini mendukung pembaruan mod langsung berikutnya di `Magisk Manager` (mod yang diperbarui akan berlaku tanpa me-reboot perangkat).

### pembaruan kernel

Modul ini mencakup kernel berikut:

- [clash](https://github.com/Dreamacro/clash)
- [clash.meta](https://github.com/MetaCubeX/Clash.Meta)
- [sing-box](https://github.com/SagerNet/sing-box)
- [v2ray-core](https://github.com/v2fly/v2ray-core)
- [Xray-core](https://github.com/XTLS/Xray-core)

Konfigurasi yang sesuai dengan kernel adalah `${bin_name}`, yang dapat diatur ke ( `clash` | `xray` | `v2ray` | `sing-box`).

Setiap core bekerja di direktori `/data/adb/box/bin/${bin_name}`, nama core ditentukan oleh `bin_name` di file `/data/adb/box/settings.ini`.

Pastikan Anda terhubung ke internet dan jalankan perintah berikut untuk memperbarui file kernel:

```sh
# perbarui kernel yang dipilih, sesuai dengan `bin_name`
su -c /data/adb/box/scripts/box.tool upcore
```

Jika Anda menggunakan `clash/sing-box` sebagai kernel yang dipilih, Anda mungkin juga perlu menjalankan perintah berikut untuk membuka panel kontrol:

```sh
# Perbarui panel admin clash/sing-box
su -c /data/adb/box/scripts/box.tool upyacd
```

Alternatifnya, Anda dapat melakukannya sekaligus (yang mungkin menghabiskan ruang penyimpanan secara tidak perlu):

```sh
# Perbarui semua file (termasuk berbagai jenis kernel)
su -c /data/adb/box/scripts/box.tool all
```

## Konfigurasi

**Layanan inti berikut disebut sebagai BFM**

- Layanan inti berikut secara kolektif disebut sebagai BFM
- Anda dapat mengaktifkan atau menonaktifkan modul untuk memulai atau menghentikan layanan BFM secara real time melalui aplikasi Magisk Manager tanpa harus me-reboot perangkat. Memulai layanan mungkin memerlukan waktu beberapa detik, penghentian layanan akan segera berlaku.

### konfigurasi inti

- Untuk konfigurasi inti `bin_name`, silakan lihat bagian **Pembaruan Kernel** untuk konfigurasi
- Setiap file konfigurasi inti perlu dikustomisasi oleh pengguna, dan skrip akan memeriksa validitas konfigurasi, dan hasil pemeriksaan akan disimpan di file `/data/adb/box/run/runs.log`.
- Tip: Baik `clash` dan `sing-box` datang dengan pra-konfigurasi dengan skrip proxy transparan. Untuk konfigurasi lebih lanjut, silakan merujuk ke dokumentasi resmi. Alamat: [dokumen resmi Clash](https://github.com/Dreamacro/clash/wiki/configuration), [dokumen resmi sing-box](https://sing-box.sagernet.org/configuration/outbound/)

### Menerapkan pemfilteran (blacklist/whitelist)

- BFM menyediakan proxy untuk semua aplikasi (APP) dari semua pengguna Android secara default.
- Jika Anda ingin BFM mem-proxy semua aplikasi (APP), kecuali beberapa aplikasi, silakan buka file /data/adb/box/settings.ini, ubah nilai proxy_mode menjadi blacklist (default), tambahkan aplikasi yang akan dikecualikan ke packages_list , misalnya: packages_list=("com.termux" "org.telegram.messenger")
- Jika Anda hanya ingin mem-proxy aplikasi (APP) tertentu, gunakan whitelist.
- Ketika nilai proxy_mode adalah TUN, proxy transparan tidak akan berfungsi, dan hanya kernel yang sesuai yang akan mulai mendukung TUN. Saat ini, hanya clash dan sing-box yang tersedia.
- Jika Clash digunakan, blacklist dan whitelist tidak akan berlaku dalam mode fake-ip.

### mengubah mode proxy

- BFM menggunakan TPROXY untuk mem-proxy TCP+UDP secara transparan (default). Jika terdeteksi bahwa perangkat tidak mendukung TPROXY, buka /data/adb/box/settings.ini dan ubah network_mode="redirect" menjadi REDIRECT yang hanya menggunakan proxy TCP.
- Buka file /data/adb/box/settings.ini dan ubah nilai network_mode menjadi redirect, tproxy atau mixed.
- redirect：redirect(TCP).
- tproxy：tproxy(TCP + UDP).
- mixed：redirect(TCP) and tun(UDP).

### Lewati proxy transparan saat menghubungkan ke Wi-Fi atau hotspot

- BFM secara transparan memproksi localhost dan hotspot (termasuk tethering USB) secara default.
- Buka file /data/adb/box/settings.ini, ubah ignore_out_list dan tambahkan wlan+, sehingga proxy transparan akan mem-bypass wlan dan hotspot tidak akan terhubung ke proxy.
- Buka file /data/adb/box/settings.ini, ubah ap_list dan tambahkan wlan+. BFM akan mem-proxy hotspot (mungkin ap+ / wlan+ untuk perangkat Mediatek).
- Gunakan perintah ifconfig di Terminal untuk mengetahui nama AP.

### Aktifkan Cron Job untuk memperbarui Geo dan Subs sesuai jadwal secara otomatis

- Buka file /data/adb/box/settings.ini, ubah nilai run_crontab=true, dan atur interva_update="@daily" (default), sesuaikan dengan yang anda inginkan.

```shell
  # jalankan perintah
  su -c /data/adb/box/scripts/box.service cron
```

- Maka secara otomatis Geox dan Subs akan diperbarui sesuai jadwal interva_update.

## Mulai dan Berhenti

### Masuk ke mode manual

- Jika Anda ingin memiliki kontrol penuh atas BFM dengan menjalankan perintah, buat saja file baru bernama /data/adb/box/manual. Dalam hal ini, layanan BFM tidak akan dimulai secara otomatis saat perangkat Anda dihidupkan, Anda juga tidak dapat mengatur mulai atau berhentinya layanan melalui aplikasi Magisk Manager.

### Memulai dan menghentikan layanan manajemen

- Skrip layanan BFM adalah /data/adb/box/scripts/box.service

- Mulai BFM:

```shell
  su -c /data/adb/box/scripts/box.service start
```

- Hentikan BFM:

```shell
  su -c /data/adb/box/scripts/box.service stop
```

- Terminal akan mencetak log pada saat yang sama dan mengeluarkannya ke file log.

#### Kelola apakah proxy transparan diaktifkan

- Skrip proxy transparan adalah /data/adb/box/scripts/box.iptables.

- Aktifkan proxy transparan:

```shell
  su -c /data/adb/box/scripts/box.iptables enable
```

- Nonaktifkan proxy transparan:

```shell
  su -c /data/adb/box/scripts/box.iptables disable
```

## Langganan dan pembaruan basis data Geo

Anda dapat memperbarui langganan dan basis data Geo secara bersamaan menggunakan perintah berikut:

```shell
  su -c /data/adb/box/scripts/box.tool geosub
```

Atau Anda dapat memperbaruinya satu per satu.

### perbarui langganan

```shell
  su -c /data/adb/box/scripts/box.tool subs
```

### Perbarui basis data Geo

```shell
  su -c /data/adb/box/scripts/box.tool geox
```

## instruksi lainnya

- Saat memodifikasi salah satu file konfigurasi inti, pastikan konfigurasi terkait tproxy cocok dengan definisi di file /data/adb/box/settings.ini.
- Jika perangkat memiliki alamat IP publik, tambahkan IP tersebut ke jaringan internal di file /data/adb/box/scripts/box.iptables untuk mencegah pengulangan lalu lintas.
- Log untuk layanan BFM dapat ditemukan di direktori /data/adb/box/run.

Anda dapat menjalankan perintah berikut untuk mendapatkan instruksi operasi terkait lainnya:

```shell
  su -c /data/adb/box/scripts/box.tool
  # usage: $0 {rconf|reload|upyacd|upcore|upyq|cgroup|port|geox|subs|geosub|all}
  su -c /data/adb/box/scripts/box.service
  # usage: $0 {start|stop|restart|usage|cron}
  su -c /data/adb/box/scripts/box.iptables
  # usage: $0 {enable|disable|renew}
```

## uninstall

- Instalasi yang menghapus modul ini dari Magisk Manager, akan menghapus file /data/adb/service.d/box_service.sh dan direktori data BFM di /data/adb/box.
- Anda dapat menghapus data BFM dengan perintah berikut:

```shell
  su -c rm -rf /data/adb/box
```

```shell
  su -c rm -rf /data/adb/service.d/box_service.sh
```