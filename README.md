```markdown
![Laravel]<img href="https://img.shields.io/badge/laravel-%23FF2D20.svg?style=for-the-badge&logo=laravel&logoColor=white">
![Docker]<img href="https://img.shields.io/badge/docker-%230db7ed.svg?style=for-the-badge&logo=docker&logoColor=white">
![Arch Linux]<img href="https://img.shields.io/badge/Arch%20Linux-1793D1?style=for-the-badge&logo=arch-linux&logoColor=white">
```

# 🚀 Laravel x Docker Sail (Arch Linux Setup)

Dokumentasi instalasi dan konfigurasi pengembangan Laravel menggunakan **Docker Sail** pada sistem operasi berbasis Arch Linux / CachyOS dengan **Fish Shell**.

---

## 🛠 1. Konfigurasi Awal Sistem
Pastikan Docker terinstall dan user kamu memiliki izin akses tanpa `sudo`.

```bash
# Update & Install Docker
sudo pacman -Syu docker docker-compose github-cli

# Jalankan & Aktifkan Service
sudo systemctl enable --now docker

# Pengaturan User Group
sudo groupadd docker
sudo usermod -aG docker $USER
```
***Catatan:*** Lakukan `reboot` sistem setelah menjalankan perintah di atas agar perubahan grup user tersinkronisasi sepenuhnya.

## 🏗 2. Instalasi Laravel

Gunakan skrip `curl` resmi Laravel untuk membangun lingkungan awal.
```bash
curl -s "[https://laravel.build/nama-proyek](https://laravel.build/nama-proyek)" | bash
cd nama-proyek
```

## 🐟 3. Konfigurasi Fish Shell (Alias Sail)

Agar pemanggilan perintah lebih efisien pada `Fish Shell`, buat fungsi alias permanen:
```bash
# Membuat alias
alias sail='[ -f sail ] && sh sail; or sh vendor/bin/sail'

# Menyimpan alias agar permanen
funcsave sail
```
Sekarang kamu cukup mengetik sail (contoh: sail up) di terminal.

## 🐳 4. Manajemen Kontainer & Database

Menjalankan Layanan
`sail up -d`
Akses aplikasi melalui: <a href="http://localhost">http://localhost</a>

Konfigurasi phpMyAdmin

Tambahkan blok kode ini di dalam file `compose.yaml` pada bagian `services:` untuk akses GUI Database:
```yaml
phpmyadmin:
    image: 'phpmyadmin:latest'
    ports:
        - '8080:80'
    networks:
        - sail
    environment:
        PMA_ARBITRARY: 1
        PMA_HOST: mysql
        PMA_USER: root
        PMA_PASSWORD: '${DB_PASSWORD}'
    depends_on:
        - mysql
```
Akses phpMyAdmin melalui: <a href="http://localhost:8080"> http://localhost:8080 </a>
Inisialisasi Database
```bash
sail artisan migrate
```
## 🐙 5. Integrasi GitHub

Gunakan GitHub CLI untuk sinkronisasi proyek ke repository remote.
```bash
# Login ke GitHub
gh auth login

# Inisialisasi Git Lokal
git init
git add .
git commit -m "Initial commit: Laravel Sail on Arch Linux"

# Buat Repository & Push
gh repo create nama-proyek --public --source=. --remote=origin --push
```

## 📋 6. Cheat Sheet Perintah Penting

| Perintah | Deskripsi |
| :--- | :--- |
| `sail up -d` | Menjalankan kontainer di background. |
| `sail stop` | Menghentikan kontainer sementara. |
| `sail down` | Mematikan kontainer & menghapus network. |
| `sail artisan ...` | Menjalankan perintah Artisan Laravel. |
| `sail composer ...` | Menjalankan perintah PHP Composer. |
| `sail npm run dev` | Menjalankan Vite untuk aset frontend. |
| `sail shell` | Masuk ke terminal di dalam kontainer. |
| `docker stats` | Memantau penggunaan resource (RAM/CPU). |
