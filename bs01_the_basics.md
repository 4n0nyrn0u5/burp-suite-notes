# 🔧 Burp Suite: The Basics (Asoslar)

> **Maqsad:** Burp Suite ning asosiy interfeysi, sozlamalari va Proxy funksiyasi bilan tanishish.

---

## 📖 Burp Suite nima?

**Burp Suite** — veb-ilovalarni pentest qilish uchun eng keng tarqalgan professional vosita. PortSwigger tomonidan ishlab chiqilgan.

### Versiyalar:

| Versiya | Narxi | Xususiyat |
|---------|-------|-----------|
| **Community** | Bepul | Asosiy funksiyalar, Intruder sekin |
| **Professional** | Pullik | Hamma funksiyalar, tez Intruder, Scanner |
| **Enterprise** | Pullik | Avtomatik skan, katta kompaniyalar uchun |

---

## 🖥️ Asosiy Modullar (Tabs)

| Modul | Vazifasi |
|-------|---------|
| **Proxy** | Brauzer va server orasida so'rovlarni ushlab olish |
| **Repeater** | So'rovlarni qayta-qayta yuborish va tahrirlash |
| **Intruder** | So'rovlarni avtomatik fuzz qilish |
| **Decoder** | Ma'lumotlarni encode/decode qilish |
| **Comparer** | Ikki so'rov/javobni solishtirish |
| **Sequencer** | Token tasodifiyligini tekshirish |
| **Scanner** | Avtomatik zaiflik skaneri (faqat Pro) |
| **Extensions** | Qo'shimcha plaginlar (BApp Store) |

---

## 🌐 Proxy — Asosiy Funksiya

### Proxy nima?
Burp Suite brauzer va server orasida **vositachi** (proxy — oʻrtachi) bo'lib turadi. Barcha HTTP/HTTPS so'rovlarni ushlab oladi, ko'rish va tahrirlash imkonini beradi.

```
Brauzer → [Burp Suite Proxy] → Server
Brauzer ← [Burp Suite Proxy] ← Server
```

### Proxy sozlash:

**1. Burp Suite da:**
```
Proxy → Options → Proxy Listeners
→ 127.0.0.1:8080 (standart)
```

**2. Brauzerda (Firefox):**
```
Settings → Network Settings → Manual Proxy:
HTTP Proxy: 127.0.0.1
Port: 8080
✅ "Use this proxy for HTTPS too"
```

**3. FoxyProxy (eng qulay usul):**
```
1. Firefox → Extensions → FoxyProxy o'rnating
2. FoxyProxy → Add:
   Title: Burp
   Proxy IP: 127.0.0.1
   Port: 8080
3. Kerak bo'lganda yoqing/o'chiring
```

---

## 🔐 HTTPS Sertifikat O'rnatish

HTTPS saytlarni ushlab olish uchun Burp ning sertifikatini brauzerga o'rnatish kerak.

```
1. Burp Suite yoqilgan holda brauzerda:
   http://burp  yoki  http://127.0.0.1:8080
   
2. "CA Certificate" tugmasini bosing → cacert.der yuklab oling

3. Firefox:
   Settings → Privacy & Security → Certificates
   → "View Certificates" → "Authorities" tab
   → "Import" → cacert.der ni tanlang
   → ✅ "Trust this CA to identify websites"
   
4. Endi HTTPS saytlar ham ushlanadi!
```

---

## 🎯 Intercept (Ushlab Olish)

```
Proxy → Intercept → "Intercept is ON"

# So'rov ushlanganida:
Forward    → So'rovni servarga yuboring (o'zgarishsiz)
Drop       → So'rovni o'chiring (serverga yubormang)
Action     → So'rovni boshqa modulga yuboring
```

### Muhim tugmalar:
```
CTRL+F    → Forward (keyingi so'rovga o'tish)
CTRL+R    → Repeater ga yuborish
CTRL+I    → Intruder ga yuborish
```

---

## 📜 HTTP History (Tarix)

```
Proxy → HTTP History

# Barcha o'tgan so'rovlar bu yerda saqlanadi
# Intercept OFF bo'lsa ham!
# Filter orqali qidiring:
- Faqat POST so'rovlar
- Faqat ma'lum domen
- Faqat ma'lum status kod
```

---

## 🔍 So'rovni O'qish

```http
# So'rov (Request) tuzilishi:
POST /login HTTP/1.1          ← Metod, URL, HTTP versiyasi
Host: target.com              ← Maqsad server
Cookie: session=abc123        ← Cookie
Content-Type: application/x-www-form-urlencoded
Content-Length: 27

username=admin&password=test  ← So'rov tanasi (body)

# Javob (Response) tuzilishi:
HTTP/1.1 200 OK               ← Status
Set-Cookie: session=xyz789    ← Yangi cookie
Content-Type: text/html

<html>...</html>              ← Javob tanasi
```

---

## 🛠️ Target Tab (Nishon)

```
Target → Site Map
# Burp ko'rgan barcha URL lar daraxt shaklida

Target → Scope (Doira)
# Faqat ma'lum sayt bilan ishlash uchun:
1. "Add" → target URL ni kiriting
2. "Use advanced scope control" ✅
3. Endi faqat shu sayt tarixi saqlanadi
```

---

## 💡 Foydali Sozlamalar

```
# Dark mode (qorong'i rejim):
User options → Display → Look and feel → Dark

# So'rovni brauzerda ochish:
So'rov ustida o'ng klik → "Request in browser"

# Qidirish:
CTRL+F → Har qanday so'rov/javobda matn qidirish

# Keyboard shortcuts:
CTRL+Z    → Bekor qilish
CTRL+A    → Hammasini tanlash
CTRL+U    → URL decode
CTRL+H    → HTML decode
```

---

## 🎯 Amaliy Misol: Birinchi Intercept

```
1. Burp Suite oching → Proxy → Intercept ON

2. Brauzerda (FoxyProxy yoqilgan):
   http://target.com/login ga kiring

3. Burp da so'rov ushlanadi:
   POST /login HTTP/1.1
   Host: target.com
   
   username=test&password=test

4. So'rovni tahrirlang:
   username=admin&password=test → admin'-- - ga o'zgartiring

5. Forward bosing → O'zgartirilgan so'rov serverga ketadi

6. Javobni ko'ring → Admin sifatida kirganmisiz?
```

---

## 💡 Eslab Qolish Uchun

- **Proxy** = Brauzer ↔ Server orasida vositachi
- **Intercept ON** = So'rovlar ushlanadi | **OFF** = O'tib ketadi
- **HTTP History** = Barcha so'rovlar tarixi (intercept off bo'lsa ham)
- **FoxyProxy** = Brauzer proxy ni tez yoqish/o'chirish
- **cacert.der** = HTTPS ushlab olish uchun sertifikat
- `CTRL+R` = Repeater | `CTRL+I` = Intruder ga yuborish
- **Scope** = Faqat kerakli sayt bilan ishlash
