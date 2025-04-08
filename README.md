# 📬 راه‌اندازی ایمیل در cPanel با Cloudflare + اتصال به Outlook

این پروژه راهنمای کامل و مرحله‌به‌مرحله‌ای برای تنظیم ایمیل روی هاست‌های cPanel در حالتیه که DNS دامنه از طریق Cloudflare مدیریت می‌شه.

🎯 هدف:
- اتصال ایمیل به Outlook (یا سایر کلاینت‌ها) با استفاده از SMTP و IMAP/POP3
- اطمینان از ارسال موفق ایمیل‌ها (مثلاً به Gmail)
- جلوگیری از اسپم شدن ایمیل‌ها با تنظیم SPF / DKIM / DMARC

---

## ✅ مراحل اصلی تنظیم ایمیل

### 1. تنظیم رکوردهای DNS در Cloudflare

#### 🔸 رکوردهای A و MX:

| نوع رکورد | Name             | Value / IP              | Proxy |
|-----------|------------------|--------------------------|--------|
| A         | mail             | YOUR.SERVER.IP           | DNS only |
| A         | cpanel           | YOUR.SERVER.IP           | DNS only |
| A         | webmail          | YOUR.SERVER.IP           | DNS only |
| MX        | domain.com   | mail.yourdomain.com      | DNS only |

> 🛑 رکوردهای مربوط به ایمیل (mail, cpanel, webmail) حتماً باید **DNS Only (خاکستری)** باشن.

---

### 2. تنظیم رکورد SPF

در بخش DNS یک رکورد TXT اضافه کن:

```txt
v=spf1 +a +mx ip4:YOUR.SERVER.IP ~all
```
مثلاً برای IP 92.126.171.80:
```txt
v=spf1 +a +mx ip4:93.127.181.80 ~all
```

### 3. تنظیم رکورد DKIM
از داخل cPanel بخش:
```txt
default._domainkey.yourdomain.com
```

و DKIM را فعال کن

اگر نیاز بود، رکورد پیشنهادی TXT با نامی شبیه زیر را در Cloudflare اضافه کن:
```txt
default._domainkey.yourdomain.com
```

### 4. افزودن رکورد DMARC (اختیاری ولی مهم)
در بخش DNS رکورد زیر را اضافه کن:
```txt
Name: _dmarc
Type: TXT
Value: v=DMARC1; p=none; sp=none; adkim=r; aspf=r;

```
بعد از تست موفق، می‌تونی p=none را به p=reject تغییر بدی.


📄 بخش کامل تنظیمات + خطاها + ابزار تست + نمونه رکوردها (قابل کپی مستقیم)
## 5. تنظیمات Outlook یا سایر کلاینت‌های ایمیل

| تنظیم              | مقدار                          |
|-------------------|---------------------------------|
| Email             | test@domain.com              |
| Username          | test@domain.com              |
| Password          | رمز عبور ایمیل                  |
| Incoming Server   | mail.domain.com             |
| Incoming Port     | 993 (IMAP) / 995 (POP3)         |
| Outgoing Server   | mail.domain.com             |
| Outgoing Port     | 465 (SSL) یا 587 (TLS)          |
| Encryption        | SSL/TLS                         |
| Auth Required     | Yes                             |

---

## 🚫 خطاهای رایج و راه‌حل‌ها

### 🔻 ایمیل ارسال نمی‌شود ولی دریافت انجام می‌شود:

- رکورد `mail` در Cloudflare ابری (Proxied) است ← باید **DNS Only (خاکستری)** شود.
- رکورد SPF یا DKIM تنظیم نشده یا اشتباه است.
- IP خروجی SMTP در SPF تعریف نشده.
- Gmail ایمیل را Reject می‌کند چون فرستنده تأیید نشده است:




<br>


## 💡 نکات تکمیلی
همیشه از IP صحیح سرورت در رکورد SPF استفاده کن.

برای تست اولیه از p=none در DMARC استفاده کن.

بعد از اطمینان، سیاست‌های قوی‌تر مثل p=quarantine یا p=reject استفاده کن.

رکورد mail باید همیشه خاکستری (DNS Only) باشه، نه نارنجی (Proxied)!

<br>

## 🧠 نویسنده
####📎 تهیه شده بر اساس تجربه واقعی از راه‌اندازی و عیب‌یابی ایمیل در هاست‌های cPanel پشت Cloudflare، مخصوص اتصال به Outlook و تحویل ایمیل به Gmail.

اگر این راهنما به کارت اومد، خوشحال می‌شم ستاره بزنی ⭐ یا فورکش کنی 🙌





📎 لایسنس
استفاده آزاد، با ذکر منبع ❤️
