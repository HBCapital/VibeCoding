# FlowOne Installation Guide

> Installation and configuration guide for FlowOne CMS

## 📋 System Requirements

### Minimum Requirements (Development)

- **PHP**: 8.1+ (recommended 8.4+ for JIT performance)
- **Database**: SQLite 3.35+ (dev) or MySQL 8.0+ / MariaDB 10.6+ (production)
- **Composer**: 2.0+
- **Node.js**: 18+ (for admin UI and theme development)
- **RAM**: 512MB minimum, 1GB recommended
- **Disk**: 100MB for core, plus space for media

### Recommended (Production)

- **PHP**: 8.4+ with OPcache enabled
- **Database**: MariaDB 10.11+ or MySQL 8.0+
- **Redis**: 6.0+ (for object caching)
- **Web Server**: Nginx 1.20+ or Apache 2.4+
- **RAM**: 2GB+
- **SSL**: Let's Encrypt or commercial certificate

### Required PHP Extensions

```bash
# Check installed extensions
php -m

# Required extensions:
- pdo
- pdo_sqlite (for SQLite support)
- pdo_mysql (for MySQL/MariaDB)
- mbstring
- json
- openssl
- curl
- gd (for image processing)
- zip
- xml

# Optional but recommended:
- redis (for caching)
- imagick (better image processing)
- opcache (performance)
```

## 🚀 Quick Install

### Option 1: Using FlowOne CLI (Recommended)

```bash
# Install FlowOne CLI globally
composer global require flowone/cli

# Create new project
flowone new my-project

# Navigate to project
cd my-project

# Start development server (SQLite mode)
flowone serve

# Visit http://localhost:8000
```

### Option 2: Manual Installation

```bash
# Create project via Composer
composer create-project flowone/flowone my-project

# Navigate to project
cd my-project

# Copy environment file
cp .env.example .env

# Run installer
php flowone install

# Start dev server
php -S localhost:8000 -t public
```

## 🔧 Detailed Configuration

### 1. Database Configuration

#### SQLite (Development)

```env
# .env file
DB_DRIVER=sqlite
DB_PATH=storage/database/flowone.sqlite

# Cache
CACHE_DRIVER=file
```

SQLite database will be created automatically when running installer.

#### MySQL/MariaDB (Production)

```env
# .env file
DB_DRIVER=mysql
DB_HOST=localhost
DB_PORT=3306
DB_DATABASE=flowone
DB_USERNAME=flowone_user
DB_PASSWORD=your_secure_password

# Cache
CACHE_DRIVER=redis
REDIS_HOST=localhost
REDIS_PORT=6379
```

**Create database:**

```sql
-- MySQL/MariaDB
CREATE DATABASE flowone CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
CREATE USER 'flowone_user'@'localhost' IDENTIFIED BY 'your_secure_password';
GRANT ALL PRIVILEGES ON flowone.* TO 'flowone_user'@'localhost';
FLUSH PRIVILEGES;
```

### 2. Web Server Configuration

#### Nginx

```nginx
server {
    listen 80;
    server_name example.com;
    root /var/www/flowone/public;

    index index.php;

    # Security headers
    add_header X-Frame-Options "SAMEORIGIN" always;
    add_header X-Content-Type-Options "nosniff" always;
    add_header X-XSS-Protection "1; mode=block" always;

    # Gzip compression
    gzip on;
    gzip_types text/plain text/css application/json application/javascript text/xml application/xml;

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    location ~ \.php$ {
        fastcgi_pass unix:/var/run/php/php8.4-fpm.sock;
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME $realpath_root$fastcgi_script_name;
        include fastcgi_params;
    }

    # Deny access to sensitive files
    location ~ /\. {
        deny all;
    }

    location ~ /storage {
        deny all;
    }

    # Cache static assets
    location ~* \.(jpg|jpeg|png|gif|ico|css|js|woff|woff2|ttf|svg)$ {
        expires 1y;
        add_header Cache-Control "public, immutable";
    }
}
```

#### Apache

```apache
<VirtualHost *:80>
    ServerName example.com
    DocumentRoot /var/www/flowone/public

    <Directory /var/www/flowone/public>
        Options -Indexes +FollowSymLinks
        AllowOverride All
        Require all granted
    </Directory>

    # Security headers
    Header always set X-Frame-Options "SAMEORIGIN"
    Header always set X-Content-Type-Options "nosniff"
    Header always set X-XSS-Protection "1; mode=block"

    ErrorLog ${APACHE_LOG_DIR}/flowone_error.log
    CustomLog ${APACHE_LOG_DIR}/flowone_access.log combined
</VirtualHost>
```

**.htaccess** (included in `public/`):

```apache
<IfModule mod_rewrite.c>
    RewriteEngine On
    RewriteCond %{REQUEST_FILENAME} !-f
    RewriteCond %{REQUEST_FILENAME} !-d
    RewriteRule ^ index.php [L]
</IfModule>
```

### 3. PHP Configuration

#### php.ini Optimizations

```ini
; Performance
opcache.enable=1
opcache.memory_consumption=256
opcache.interned_strings_buffer=16
opcache.max_accelerated_files=10000
opcache.validate_timestamps=0  ; Production only

; Upload limits
upload_max_filesize=64M
post_max_size=64M
max_execution_time=300
memory_limit=256M

; Security
expose_php=Off
display_errors=Off  ; Production only
log_errors=On
```

### 4. Permissions

```bash
# Set correct ownership (replace www-data with your web server user)
chown -R www-data:www-data /var/www/flowone

# Set directory permissions
find /var/www/flowone -type d -exec chmod 755 {} \;

# Set file permissions
find /var/www/flowone -type f -exec chmod 644 {} \;

# Writable directories
chmod -R 775 storage/
chmod -R 775 public/uploads/
```

## 🎨 Admin UI Setup

```bash
# Install Node.js dependencies
npm install

# Development mode (with hot reload)
npm run dev

# Production build
npm run build
```

## 🔐 Security Hardening

### 1. Generate Application Key

```bash
php flowone key:generate
```

This will generate a secure `APP_KEY` in your `.env` file used for encryption.

### 2. SSL/TLS Configuration

```bash
# Install Certbot (Let's Encrypt)
sudo apt install certbot python3-certbot-nginx

# Obtain certificate
sudo certbot --nginx -d example.com -d www.example.com

# Auto-renewal (cron job)
0 12 * * * /usr/bin/certbot renew --quiet
```

### 3. Firewall Configuration

```bash
# UFW (Ubuntu)
sudo ufw allow 22/tcp    # SSH
sudo ufw allow 80/tcp    # HTTP
sudo ufw allow 443/tcp   # HTTPS
sudo ufw enable
```

### 4. Disable Directory Listing

Already handled in Nginx/Apache configs above.

## 📦 Plugin & Theme Installation

### Install Plugin

```bash
# From marketplace
flowone plugin:install seo-optimizer

# From local directory
flowone plugin:install ./my-plugin

# From Composer
composer require flowone/plugin-seo
```

### Install Theme

```bash
# From marketplace
flowone theme:install modern-blog

# From local directory
flowone theme:install ./my-theme

# Activate theme
flowone theme:activate modern-blog
```

## 🔄 Migration Commands

```bash
# Run all pending migrations
php flowone migrate

# Rollback last migration
php flowone migrate:rollback

# Reset database (⚠️ destructive)
php flowone migrate:reset

# Seed database with sample data
php flowone db:seed
```

## 🛠️ Troubleshooting

### SQLite Permission Error

```bash
# Ensure storage directory is writable
chmod 775 storage/database/
chown www-data:www-data storage/database/
```

### OPcache Not Working

```bash
# Check if enabled
php -i | grep opcache

# Restart PHP-FPM
sudo systemctl restart php8.4-fpm
```

### 500 Internal Server Error

```bash
# Check error logs
tail -f storage/logs/flowone.log

# Enable debug mode (development only)
# In .env:
APP_DEBUG=true
```

### Composer Memory Limit

```bash
# Increase memory for Composer
php -d memory_limit=-1 $(which composer) install
```

## 📊 Performance Optimization

### Enable OPcache

Already covered in PHP configuration above.

### Redis Caching

```bash
# Install Redis
sudo apt install redis-server

# Enable Redis in .env
CACHE_DRIVER=redis
REDIS_HOST=localhost
REDIS_PORT=6379
```

### CDN Integration

```env
# .env
CDN_URL=https://cdn.example.com
ASSET_URL=${CDN_URL}
```

## 🔄 Updates

### Update Core

```bash
# Check for updates
flowone update:check

# Update to latest version
flowone update

# Update with Composer
composer update flowone/core
```

### Backup Before Update

```bash
# Backup database
php flowone backup:db

# Backup files
tar -czf flowone-backup-$(date +%Y%m%d).tar.gz /var/www/flowone
```

## 📝 Environment Variables Reference

```env
# Application
APP_NAME=FlowOne
APP_ENV=production  # development, staging, production
APP_KEY=base64:xxxxx  # Generated by flowone key:generate
APP_DEBUG=false
APP_URL=https://example.com

# Database
DB_DRIVER=mysql  # sqlite, mysql, mariadb
DB_HOST=localhost
DB_PORT=3306
DB_DATABASE=flowone
DB_USERNAME=flowone_user
DB_PASSWORD=secret

# SQLite (if DB_DRIVER=sqlite)
DB_PATH=storage/database/flowone.sqlite

# Cache
CACHE_DRIVER=redis  # file, redis
REDIS_HOST=localhost
REDIS_PORT=6379
REDIS_PASSWORD=null

# Queue
QUEUE_DRIVER=redis  # sync, redis, rabbitmq

# Mail
MAIL_DRIVER=smtp
MAIL_HOST=smtp.mailtrap.io
MAIL_PORT=2525
MAIL_USERNAME=null
MAIL_PASSWORD=null
MAIL_FROM_ADDRESS=noreply@example.com
MAIL_FROM_NAME="${APP_NAME}"

# Storage
STORAGE_DRIVER=local  # local, s3
AWS_ACCESS_KEY_ID=
AWS_SECRET_ACCESS_KEY=
AWS_DEFAULT_REGION=us-east-1
AWS_BUCKET=

# CDN
CDN_URL=
```

---

**Next Steps**:

- ✅ Installation complete? Read [ARCHITECTURE.md](./ARCHITECTURE.md) to understand the system
- 🚀 Start building: See [Developer Guide](./.ai/DEVELOPER_GUIDE.md)
- 🔌 Create plugins: See [Plugin Development](./.ai/PLUGIN_DEV.md)
