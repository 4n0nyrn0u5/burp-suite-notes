# 🔁 Burp Suite: Repeater (Takrorlovchi)

> **Maqsad:** HTTP so'rovlarni qayta-qayta yuborib, har safar o'zgartirib, server javobini real vaqtda ko'rish.

---

## 📖 Repeater nima?

**Repeater** — bitta HTTP so'rovni xohlagancha tahrirlash va qayta yuborish imkonini beruvchi modul. Zaifliklarni qo'lda tekshirishning asosiy vositasi.

### Qachon ishlatiladi?
- SQL injection payloadlarini sinab ko'rish
- XSS payloadlarini tekshirish
- Parametrlarni o'zgartirib IDOR topish
- Autentifikatsiyani chetlab o'tishni sinash
- API endpoint larni o'rganish

---

## 🖥️ Repeater Interfeysi

```
┌─────────────────────────────────────────────┐
│  1 │ 2 │ 3 │  +  ← Tablar (har so'rov uchun)│
├──────────────────┬──────────────────────────┤
│   REQUEST        │      RESPONSE            │
│                  │                          │
│  POST /login     │  HTTP/1.1 200 OK         │
│  Host: target    │  Content-Type: html      │
│                  │                          │
│  user=admin      │  Welcome, admin!         │
│                  │                          │
├──────────────────┴──────────────────────────┤
│  [Send]  [Cancel]  ◄ ►  (tarix)            │
└─────────────────────────────────────────────┘
```

---

## 🚀 Repeater ga So'rov Yuborish

### Usul 1: Proxy dan
```
1. Proxy → HTTP History
2. So'rov ustida O'ng klik
3. "Send to Repeater" (yoki CTRL+R)
4. Repeater tabiga o'ting
```

### Usul 2: Intercept dan
```
1. So'rov ushlanganida
2. CTRL+R bosing
3. Forward bosing (so'rovni o'tkazib yuboring)
4. Repeater da ishlang
```

### Usul 3: Qo'lda
```
Repeater → "+" tugmasi → Bo'sh tab
So'rovni o'zingiz yozing
```

---

## ✏️ So'rovni Tahrirlash va Yuborish

```http
# Asl so'rov:
GET /user?id=5 HTTP/1.1
Host: target.com
Cookie: session=abc123

# O'zgartirish → id=5 ni id=1 ga:
GET /user?id=1 HTTP/1.1
Host: target.com
Cookie: session=abc123

# Send bosing → Javobni o'ng tomonda ko'ring
```

### Klaviatura yorliqlari:
```
CTRL+Enter   → Send (yuborish)
CTRL+Z       → Bekor qilish (undo)
CTRL+Y       → Qaytarish (redo)
CTRL+A       → Hammasini tanlash
CTRL+U       → URL decode/encode
CTRL+H       → HTML decode
CTRL+B       → Base64 decode
CTRL+Space   → Autocomplete
```

---

## 📊 Javob Ko'rinishlari (Response Views)

```
Pretty   → Formatlangan, o'qilishi oson
Raw      → Xuddi kelgan holda
Hex      → Hexadecimal (ikkilik fayllar uchun)
Render   → HTML ni brauzer kabi ko'rsatish
```

---

## 🔄 So'rovlar Tarixi (History)

```
# Repeater dagi ◄ ► tugmalar:
◄  → Oldingi yuborilgan so'rov
►  → Keyingi yuborilgan so'rov

# Shunday qilib barcha urinishlaringizni solishtira olasiz!
```

---

## 🎯 Amaliy Misollar

### Misol 1: SQL Injection Tekshirish
```http
# Asl so'rov:
POST /login HTTP/1.1
Host: target.com
Content-Type: application/x-www-form-urlencoded

username=admin&password=test

# 1-urinish: Tirnoq bilan sinash
username=admin'&password=test
# Javob: SQL xatosi? → Zaiflik bor!

# 2-urinish: SQL bypass
username=admin'-- -&password=anything
# Javob: Welcome admin? → Muvaffaqiyat!

# 3-urinish: UNION
username=' UNION SELECT 1,2,3-- -&password=x
# Javob: Qanday ma'lumot chiqdi?
```

### Misol 2: IDOR Tekshirish
```http
# Asl so'rov (o'z profilingiz):
GET /api/user/1337 HTTP/1.1
Host: target.com
Cookie: session=YOUR_SESSION

# Javob: {"id":1337,"name":"Siz","email":"siz@mail.com"}

# O'zgartiring → boshqa ID:
GET /api/user/1 HTTP/1.1

# Javob: {"id":1,"name":"Admin","email":"admin@mail.com"}
# → IDOR topildi!
```

### Misol 3: Header Manipulyatsiyasi
```http
# Admin panel faqat mahalliy IP dan kirsa:
GET /admin HTTP/1.1
Host: target.com
X-Forwarded-For: 127.0.0.1     ← Qo'shing!
X-Real-IP: 127.0.0.1           ← Yoki bu
X-Original-IP: 127.0.0.1

# Agar server bu headerlarni ishonsa → admin panel ochiladi!
```

### Misol 4: Metod O'zgartirish
```http
# GET → POST ga o'zgartirish:
GET /update?name=test HTTP/1.1
↓
POST /update HTTP/1.1
Content-Type: application/x-www-form-urlencoded

name=test

# Ba'zan server GET da cheklasa, POST da ishlatadi!
```

---

## 🔧 Foydali Funksiyalar

### Inspector Panel (O'ng Yon Panel)
```
So'rovni yubormasdan parametrlarni ko'rish va tahrirlash:
- Query Parameters (URL parametrlar)
- Body Parameters (Body parametrlar)  
- Request Cookies (Cookie lar)
- Request Headers (Sarlavhalar)
```

### O'ng Klik Menyusi
```
So'rov matnida o'ng klik:
- Send to Intruder     → Fuzz qilish uchun
- Send to Decoder      → Encode/decode
- Send to Comparer     → Solishtirish
- Insert Collaborator  → Blind hujumlar uchun (Pro)
```

---

## 💡 Eslab Qolish Uchun

| Vaziyat | Nima qilish |
|---------|------------|
| So'rovni Repeater ga | `CTRL+R` |
| Yuborish | `CTRL+Enter` |
| Oldingi so'rov | `◄` tugmasi |
| URL decode | `CTRL+U` |
| HTML ko'rish | Response → Render |
| Parametr tahrirlash | Inspector paneli |

- **Repeater** = Bir so'rovni xohlagancha o'zgartirb yuborish
- Har bir **tab** = alohida so'rov
- **◄ ►** = Yuborilgan so'rovlar tarixi
- `Pretty` = O'qilishi oson | `Raw` = Xom holat | `Render` = Brauzer ko'rinishi
- **Inspector** = Parametrlarni qulay tahrirlash
