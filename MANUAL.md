# 📚 راهنمای دستی V2Ray - Bolt Pattern

## فهرست مطالب
1. [مقدمه](#مقدمه)
2. [نصب و راه‌اندازی](#نصب-و-راه‌اندازی)
3. [ساختار Config](#ساختار-config)
4. [راهنمای تفصیلی هر بخش](#راهنمای-تفصیلی-هر-بخش)
5. [نکات مهم](#نکات-مهم)
6. [رفع مشکلات](#رفع-مشکلات)

---

## مقدمه

**V2Ray** یک پروتکل ارتباطی پیشرفته است که برای:
- 🔒 رمزنگاری ترافیک
- 🌍 دور زدن محدودیت‌های جغرافیایی
- 🛡️ افزایش امنیت شبکه

استفاده می‌شود.

### پیش‌نیازها:
- Linux یا macOS (یا Windows با WSL)
- دسترسی root/sudo
- پورت‌های مورد نظر باز و فارغ
- فایل `config.json` قابل دسترس

---

## نصب و راه‌اندازی

### مرحله 1: دانلود V2Ray

```bash
# دانلود script رسمی
curl -O https://raw.githubusercontent.com/v2fly/v2ray-core/main/release/install-release.sh

# اجرا با دسترسی root
sudo bash install-release.sh

# بررسی نصب
v2ray --version
```

### مرحله 2: قرار دادن Config

```bash
# پوشه پیکربندی را ایجاد کنید
sudo mkdir -p /etc/v2ray

# فایل config.json را کپی کنید
sudo cp config.json /etc/v2ray/config.json

# دسترسی صحیح را تنظیم کنید
sudo chown -R v2ray:v2ray /etc/v2ray
sudo chmod 755 /etc/v2ray
```

### مرحله 3: شروع سرویس

```bash
# فعال کردن سرویس در بوت
sudo systemctl enable v2ray

# شروع V2Ray
sudo systemctl start v2ray

# بررسی وضعیت
sudo systemctl status v2ray

# مشاهده لاگ‌ها
sudo journalctl -u v2ray -f
```

---

## ساختار Config

فایل `config.json` دارای ساختار زیر است:

```
config.json
├── log          (تنظیمات لاگ)
├── inbounds     (ورودی‌های پروتکل)
├── outbounds    (خروجی‌های پروتکل)
├── routing      (قوانین مسیریابی)
├── dns          (تنظیمات DNS)
├── policy       (سیاست‌های اتصال)
├── stats        (آمار)
└── api          (رابط API)
```

---

## راهنمای تفصیلی هر بخش

### 1️⃣ بخش LOG (لاگ‌گیری)

```json
"log": {
  "loglevel": "info",           // سطح لاگ: debug, info, warning, error
  "access": "/var/log/v2ray/access.log",     // فایل لاگ دسترسی
  "error": "/var/log/v2ray/error.log"        // فایل لاگ خطا
}
```

**سطح‌های لاگ:**
- `debug`: تمام جزئیات (استفاده برای debug)
- `info`: اطلاعات عمومی
- `warning`: هشدارها (پیشنهاد شده)
- `error`: تنها خطاها

---

### 2️⃣ بخش INBOUNDS (ورودی‌ها)

اینجا تعریف می‌کنید کلاینت‌ها چطور به سرور متصل شوند.

#### الف) VMess Protocol

```json
{
  "port": 8388,                 // پورت گوش‌دهی
  "protocol": "vmess",          // پروتکل
  "settings": {
    "clients": [
      {
        "id": "b831381d-6324-4d53-ad4f-8cda48b30811",  // UUID کلاینت
        "level": 1,                                      // سطح کاربر
        "alterId": 64                                    // تعداد تقلبی
      }
    ]
  },
  "streamSettings": {
    "network": "tcp",           // پروتکل انتقالی
    "security": "none"          // امنیت (none, tls, xtls)
  }
}
```

**نکات مهم:**
- **UUID**: شناسه منحصر به فرد کلاینت (برای امنیت)
- **alterId**: تعداد پیام‌های تقلبی برای کاهش تشخیص
- **security**: می‌تواند `tls` یا `xtls` برای رمزنگاری اضافی

#### ب) SOCKS Protocol

```json
{
  "port": 8389,
  "protocol": "socks",
  "settings": {
    "auth": "password",         // نوع احراز هویت
    "accounts": [
      {
        "user": "admin",        // نام کاربری
        "pass": "admin123"      // رمز عبور
      }
    ],
    "udp": true                 // پشتیبانی UDP
  }
}
```

#### ج) HTTP Protocol

```json
{
  "port": 8390,
  "protocol": "http",
  "settings": {
    "timeout": 360              // مهلت اتصال (ثانیه)
  }
}
```

---

### 3️⃣ بخش OUTBOUNDS (خروجی‌ها)

اینجا تعریف می‌کنید ترافیک خروجی چطور انجام شود.

```json
"outbounds": [
  {
    "protocol": "freedom",      // پروتکل آزاد (بدون محدودیت)
    "tag": "direct"             // برچسب برای قانون
  },
  {
    "protocol": "blackhole",    // مسدود کردن ترافیک
    "tag": "blocked"
  }
]
```

---

### 4️⃣ بخش ROUTING (مسیریابی)

تعریف می‌کند کدام ترافیک کجا برود.

```json
"routing": {
  "rules": [
    {
      "type": "field",
      "domain": ["geosite:category-ads"],    // دامنه‌های تبلیغاتی
      "outboundTag": "blocked"               // مسدود شوند
    },
    {
      "type": "field",
      "domain": ["geosite:category-ir"],     // دامنه‌های ایرانی
      "outboundTag": "direct"                // مستقیم رفتند
    }
  ]
}
```

**دسته‌بندی‌های geosite:**
- `geosite:category-ads` - تبلیغات
- `geosite:category-ir` - ایران
- `geosite:category-us` - آمریکا
- و غیره...

---

### 5️⃣ بخش DNS

```json
"dns": {
  "servers": [
    "8.8.8.8",      // Google DNS
    "8.8.4.4",      // Google DNS Backup
    "1.1.1.1",      // Cloudflare DNS
    "1.0.0.1"       // Cloudflare DNS Backup
  ]
}
```

---

### 6️⃣ بخش POLICY (سیاست)

تنظیمات پیشرفته برای اتصالات:

```json
"policy": {
  "levels": {
    "0": {
      "handshake": 4,           // مهلت دست‌دهی (ثانیه)
      "connIdle": 300,          // مهلت خاموشی (ثانیه)
      "uplinkOnly": 2,          // آپلود فقط
      "downlinkOnly": 5,        // دانلود فقط
      "bufferSize": 10240       // اندازه بافر
    }
  }
}
```

---

## نکات مهم

### 🔐 امنیت

1. **UUID تصادفی تولید کنید:**
   ```bash
   uuidgen
   ```

2. **رمز عبور قوی استفاده کنید**

3. **alterID را بالا نگه دارید** (حداقل 64)

4. **TLS/XTLS فعال کنید** برای رمزنگاری بیشتر

### 📊 بهینه‌سازی

```json
"bufferSize": 20480,      // برای سرعت بیشتر
"connIdle": 600,          // برای اتصالات طولانی‌تر
```

### 🛡️ محدودیت سرعت

```json
"uplink": 100,            // 100 Mbps
"downlink": 100           // 100 Mbps
```

---

## رفع مشکلات

### مشکل: سرویس شروع نمی‌شود

```bash
# بررسی syntax JSON
sudo v2ray test -config /etc/v2ray/config.json

# مشاهده خطا
sudo journalctl -u v2ray -n 50
```

### مشکل: پورت‌ها استفاده‌شده هستند

```bash
# بررسی پورت‌ها
sudo netstat -tlnp | grep v2ray

# یا
sudo lsof -i :8388
```

### مشکل: اتصال مشکل دارد

```bash
# بررسی فایروال
sudo ufw status
sudo ufw allow 8388
sudo ufw allow 8389
```

### مشکل: لاگ‌ها را می‌خواهید

```bash
# مشاهده لاگ‌های زمان‌واقعی
sudo tail -f /var/log/v2ray/access.log
sudo tail -f /var/log/v2ray/error.log
```

---

## دستورات مفید

```bash
# شروع دوباره
sudo systemctl restart v2ray

# توقف
sudo systemctl stop v2ray

# مشاهده وضعیت
sudo systemctl status v2ray

# غیرفعال کردن در بوت
sudo systemctl disable v2ray

# ویرایش کانفیگ
sudo nano /etc/v2ray/config.json

# تست کانفیگ
sudo v2ray test -config /etc/v2ray/config.json
```

---

## منابع مفید

- 📖 [اسناد رسمی V2Ray](https://www.v2fly.org/)
- 🐙 [مخزن GitHub V2Ray](https://github.com/v2fly/v2ray-core)
- 📝 [راهنمای کانفیگ](https://www.v2fly.org/config/overview.html)

---

**نوشته شده برای Bolt Pattern**
آخرین بروزرسانی: 2026-05-22
