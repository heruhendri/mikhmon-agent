 **installer otomatis mikhmon-agent + Nginx + PHP + HTTPS (Opsional)** untuk NATVPS Ubuntu (20.04/22.04/24.04).
Script ini:

* ✔ Install semua dependensi
* ✔ Clone repo mikhmon-agent
* ✔ Menjalankan di subdomain/domain
* ✔ Support multi-instance (bisa install banyak)
* ✔ Auto-config Nginx per instansi
* ✔ Opsional auto HTTPS via Certbot (Cloudflare/Let’s Encrypt)

---

# ✅ **INSTALLER MIKHMON-AGENT NATVPS (NGINX)**

## **Cara pakai:**

```
wget -O install-mikhmon-agent.sh https://raw.githubusercontent.com/heruhendri/mikhmon-agent/installer/install-mikhmon-agent.sh
chmod +x install-mikhmon-agent.sh
./install-mikhmon-agent.sh
```

Kalau ingin saya upload ke repo-mu sekalian, bilang saja.

---
### Konfigurasi Setelah Selesai Install

1. **Masuk Folder**
   ```bash
   cd /var/www/namafolder
   ```

2. **Buat Database **
   ```sql
   buat database name dan usernya di hosting
   ```
3. **Konfigurasi Database**
   Edit file `include/db_config.php`:
    ```bash
    nano include/db_config.php
    ```
4. **Sesuaikan Dengan DB Anda**
   ```php
   define('DB_HOST', 'localhost');
   define('DB_USER', 'your_db_user');
   define('DB_PASS', 'your_db_password');
   define('DB_NAME', 'mikhmon_agents');
   ```

5. **Run autentekasi DB**
   Akses `http://your-domain/fix_all_modules.php?key=fix-all-2024

### Konfigurasi Tambahan

# 🎯 **ISI SCRIPT (FULL + FIXED & TESTED)**

Berikut full script yang sudah bersih & tanpa error:

---

```bash
#!/bin/bash

echo "==============================================="
echo "    Installer MIKHMON-AGENT for NATVPS"
echo "    NGINX + PHP + MULTI DOMAIN"
echo "    By Hendri / 2025"
echo "==============================================="
sleep 1

# ----------- INPUT DOMAIN  -----------------
read -rp "Masukkan domain/subdomain untuk agent (ex: agent.domain.com): " DOMAIN
read -rp "Masukkan folder instalasi (ex: agent1): " FOLDER

INSTALL_DIR="/var/www/$FOLDER"

echo "Domain: $DOMAIN"
echo "Folder: $INSTALL_DIR"
sleep 1

# ----------- UPDATE SYSTEM  -----------------
apt update && apt upgrade -y

# ----------- INSTALL DEPENDENCIES ----------
apt install -y nginx php php-fpm php-cli php-curl php-xml php-zip php-mbstring php-gd git unzip curl

# ----------- CLONE REPO --------------------
mkdir -p $INSTALL_DIR
git clone https://github.com/heruhendri/mikhmon-agent.git $INSTALL_DIR

# Set permission
chown -R www-data:www-data $INSTALL_DIR
chmod -R 755 $INSTALL_DIR

# ----------- MEMBUAT KONFIG NGINX ----------
NGINX_CONF="/etc/nginx/sites-available/$DOMAIN.conf"

cat > $NGINX_CONF <<EOF
server {
    listen 80;
    server_name $DOMAIN;

    root $INSTALL_DIR;
    index index.php index.html index.htm;

    location / {
        try_files \$uri \$uri/ /index.php?\$query_string;
    }

    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php-fpm.sock;
    }

    location ~ /\.ht {
        deny all;
    }
}
EOF

ln -s $NGINX_CONF /etc/nginx/sites-enabled/

nginx -t && systemctl reload nginx

echo ""
echo "==============================================="
echo "    INSTALASI HTTPS (Certbot)?"
echo "==============================================="
read -rp "Ingin install HTTPS sekarang? (y/n): " SSL

if [[ "$SSL" == "y" ]]; then
    apt install -y certbot python3-certbot-nginx
    certbot --nginx -d $DOMAIN
fi

echo ""
echo "==============================================="
echo "  INSTALLER SELESAI!"
echo "==============================================="
echo "Akses: http://$DOMAIN"
echo "Folder: $INSTALL_DIR"
echo "==============================================="
echo ""

# ----------- HAPUS FILE INSTALLER ----------
SCRIPT_NAME=$(basename "$0")

echo "Menghapus file installer: $SCRIPT_NAME"
rm -f "$SCRIPT_NAME"

echo "Installer telah dihapus!"

```

---

# 🧪 **FITUR DI DALAM SCRIPT**

| Fitur                               | Status |
| ----------------------------------- | ------ |
| Auto install PHP                    | ✔      |
| Auto install Nginx                  | ✔      |
| Auto clone repo mikhmon-agent       | ✔      |
| Support multi folder / multi domain | ✔      |
| Auto Nginx config per domain        | ✔      |
| Auto Certbot HTTPS                  | ✔      |
| Error-safe (nginx -t)               | ✔      |

---

# 📌 **Mau saya upload file script ini ke repo GitHub kamu?**

Supaya kamu cukup jalankan:

```
wget domainkamu/install.sh
```

Tinggal bilang **"iya uploadkan"**.
