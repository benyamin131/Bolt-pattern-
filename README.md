# V2Box - Complete Configuration

کانفیگ کامل و آماده برای استفاده V2Ray/V2Box

## 📋 فایل‌های موجود

- **config.json** - کانفیگ اصلی V2Box
- **.env.example** - متغیرهای محیط
- **docker-compose.yml** - کانفیگ Docker Compose
- **Dockerfile** - Dockerfile برای ساخت image

## 🚀 نحوه استفاده

### روش 1: Docker Compose
```bash
docker-compose up -d
```

### روش 2: Docker Build
```bash
docker build -t v2box .
docker run -d -p 8388:8388 -p 8389:8389 --name v2box v2box
```

### روش 3: مستقیم
```bash
v2ray -c config.json
```

## ⚙️ پورت‌های پیش‌فرض

- **VMess**: 8388
- **SOCKS**: 8389

## 🔐 اطلاعات لاگین

**SOCKS:**
- Username: `admin`
- Password: `admin123`

**VMess UUID:**
```
b831381d-6324-4d53-ad4f-8cda48b30811
```

## 📝 تغییر کانفیگ

1. ویرایش فایل `config.json`
2. تغییر متغیرهای محیط در `.env`
3. Restart کردن سرویس

## 📊 Log Files

- **Access Log**: `/var/log/v2ray/access.log`
- **Error Log**: `/var/log/v2ray/error.log`

## 🌐 DNS

سرورهای DNS پیش‌فرض:
- 8.8.8.8 (Google)
- 8.8.4.4 (Google)
- 1.1.1.1 (Cloudflare)

## ✅ Health Check

سرویس دارای health check است و خودکار restart می‌شود در صورت مشکل.

## 📌 نکات مهم

- UUID را تغییر دهید
- پسورد SOCKS را تغییر دهید
- مقادیر DNS را نسبت به نیاز تغییر دهید
- فایل‌های log را منظم پاک کنید

---

**ایجاد شده توسط Copilot**
