# 🎯 Burp Suite: Intruder (Hujumchi)

> **Maqsad:** HTTP so'rovlarni avtomatik ravishda fuzz qilish — brute force, enumeration, fuzzing va injection uchun.

---

## 📖 Intruder nima?

**Intruder** — so'rovning bir yoki bir nechta joyiga wordlist dan qiymatlar kiritib, avtomatik yuboradigan modul. Pentesting ning asosiy avtomatlashtirish vositasi.

> ⚠️ **Community versiyasida Intruder juda sekin ishlaydi** (throttled — sekinlashtirilgan). Tez ishlash uchun **ffuf** yoki **Turbo Intruder** extension ishlatish tavsiya etiladi.

---

## 🚀 Intruder ga So'rov Yuborish

```
# Proxy → HTTP History → So'rov ustida:
O'ng klik → "Send to Intruder"
yoki CTRL+I

# Keyin Intruder tabiga o'ting
```

---

## 🎯 Attack Types (Hujum Turlari)

### 1. Sniper (Ot o'qchi) — Eng Ko'p Ishlatiladigan
```
# Bitta wordlist, bitta pozitsiya
# Agar bir nechta pozitsiya bo'lsa — navbat bilan har biriga qo'yiladi

Pozitsiya:  username=§test§&password=§pass§
Wordlist:   [admin, root, user]

Yuboriladi:
1. username=admin&password=pass    (1-pozitsiya)
2. username=root&password=pass     (1-pozitsiya)
3. username=user&password=pass     (1-pozitsiya)
4. username=test&password=admin    (2-pozitsiya)
5. username=test&password=root     (2-pozitsiya)
...

# Ishlatiladi: Username enumeration, parametr fuzzing
```

### 2. Battering Ram (Qo'chqor) 
```
# Bitta wordlist, BARCHA pozitsiyalarga bir vaqtda bir xil qiymat

Pozitsiya:  username=§test§&password=§test§
Wordlist:   [admin, root, user]

Yuboriladi:
1. username=admin&password=admin
2. username=root&password=root
3. username=user&password=user

# Ishlatiladi: Username = Password bo'lganda
```

### 3. Pitchfork (Sanchqi)
```
# Bir nechta wordlist, bir nechta pozitsiya — parallel

Pozitsiya:  username=§A§&password=§B§
Wordlist 1: [admin, john, bob]
Wordlist 2: [password1, letmein, qwerty]

Yuboriladi:
1. username=admin&password=password1
2. username=john&password=letmein
3. username=bob&password=qwerty

# Ishlatiladi: Username:password juftliklari ro'yxati
```

### 4. Cluster Bomb (Klaster Bomba)
```
# Bir nechta wordlist — barcha kombinatsiyalar

Pozitsiya:  username=§A§&password=§B§
Wordlist 1: [admin, root]     (2 ta)
Wordlist 2: [pass1, pass2]    (2 ta)

Yuboriladi: 2×2 = 4 ta so'rov
1. username=admin&password=pass1
2. username=admin&password=pass2
3. username=root&password=pass1
4. username=root&password=pass2

# Ishlatiladi: To'liq brute force
# ⚠️ Ko'p kombinatsiya = sekin!
```

---

## ✏️ Pozitsiyalarni Belgilash (Positions Tab)

```
# § § belgilari orasidagi narsa o'zgartiriladi

# Qo'lda belgilash:
1. Matnni tanlang
2. "Add §" tugmasini bosing
yoki
3. § belgisini qo'lda yozing

# "Clear §" → Barcha belgilerini o'chirish
# "Auto §" → Burp o'zi taxmin qiladi

# Misol:
POST /login HTTP/1.1
Host: target.com

username=§admin§&password=§password§
           ↑ 1-pozitsiya      ↑ 2-pozitsiya
```

---

## 📋 Payloadlar (Payloads Tab)

### Payload turlari:
```
Simple list     → Qo'lda yoziladigan ro'yxat
Runtime file    → Fayl dan o'qish (wordlist)
Numbers         → Raqamlar oralig'i (1 dan 1000 gacha)
Dates           → Sanalar
Brute forcer    → Barcha kombinatsiyalar (aaa, aab, ...)
Null payloads   → Bo'sh (race condition uchun)
Username generator → Ism/familiyadan username yasash
```

### Wordlist qo'shish:
```
Payload type: Simple list
→ "Load" → Fayl tanlang

Mashhur wordlist lar:
/usr/share/wordlists/rockyou.txt              (parollar)
/usr/share/seclists/Usernames/Names/names.txt (ismlar)
/usr/share/wordlists/dirb/common.txt          (directory)
/usr/share/seclists/Fuzzing/                  (fuzzing)
```

### Payload Processing (Qayta ishlash):
```
"Add" → qo'shimcha matn qo'shish
Prefix: admin_     → admin_john, admin_bob
Suffix: @mail.com  → john@mail.com, bob@mail.com
Match/Replace      → Almashtirish
URL encode         → Encode qilish
```

---

## ⚙️ Settings Tab (Sozlamalar)

### Request Engine:
```
Number of threads:  1 (Community) / 20+ (Pro)
Number of retries:  3
Pause between: 0ms (tez) / 2000ms (sekin — rate limit uchun)
```

### Attack Results Filtering:
```
# Muvaffaqiyatli so'rovlarni topish uchun:
Status codes: 200, 302 (redirect)
Content length: O'zgargan uzunlik
Response grep: "Welcome", "Success", "Invalid" kabi matнlar
```

### Grep - Match (Moslik Qidirish):
```
# Javobda ma'lum matn bo'lsa belgilash:
"Add" → "Welcome admin" → Intruder bu matni topsa rang beradi

# Grep - Extract:
Javobdan ma'lum qismni ajratib olish (masalan: CSRF token)
```

---

## 🎯 Amaliy Misollar

### Misol 1: Username Enumeration (Sniper)
```
# So'rov:
POST /login HTTP/1.1

username=§test§&password=wrongpassword

# Attack type: Sniper
# Payload: /usr/share/seclists/Usernames/Names/names.txt
# Grep-Match: "That username already exists"

# Start Attack → "That username already exists" bo'lganlar → username topildi!
```

### Misol 2: Parol Brute Force (Sniper)
```
# Username topildi: admin
POST /login HTTP/1.1

username=admin&password=§test§

# Payload: /usr/share/wordlists/rockyou.txt
# Grep-Match: "Welcome" yoki Status 302

# Start Attack → 302 redirect bo'lganlar → parol topildi!
```

### Misol 3: Directory Fuzzing (Sniper)
```
GET /§test§ HTTP/1.1
Host: target.com

# Payload: /usr/share/wordlists/dirb/common.txt
# Filter: Status 200, 301, 302 (404 larni o'chirish)
```

### Misol 4: IDOR (Numbers Payload)
```
GET /api/user/§1§ HTTP/1.1
Host: target.com
Cookie: session=abc123

# Payload type: Numbers
# From: 1  To: 1000  Step: 1

# Start → Status 200 bo'lganlar → mavjud userlar!
```

### Misol 5: CSRF Token bilan Brute Force
```
# Har so'rovda yangi CSRF token kerak:
1. Grep-Extract: CSRF tokenni javobdan ajrat
2. Redirections: Always follow
3. Update Content-Length: ✅
4. Macro: Har so'rov oldidan login sahifasini ol
```

---

## 🔧 Turbo Intruder (Extension — Tez variant)

Community da Intruder sekin bo'lganda:
```python
# Turbo Intruder skripti:
def queueRequests(target, wordlists):
    engine = RequestEngine(
        endpoint=target.endpoint,
        concurrentConnections=30,
        requestsPerConnection=100,
        pipeline=True
    )
    
    for word in open('/usr/share/wordlists/rockyou.txt'):
        engine.queue(target.req, word.rstrip())

def handleResponse(req, interesting):
    if '200' in req.status:
        table.add(req)
```

---

## 💡 Eslab Qolish Uchun

| Attack Type | Wordlist soni | Ishlatish |
|-------------|---------------|-----------|
| **Sniper** | 1 | Bir parametr fuzzing |
| **Battering Ram** | 1 | User=Pass kombinatsiya |
| **Pitchfork** | Ko'p | Juftliklar ro'yxati |
| **Cluster Bomb** | Ko'p | To'liq brute force |

**Eng muhim:**
- `§ §` = Pozitsiya belgilari
- **Grep-Match** = Muvaffaqiyatni aniqlaash matni
- **Numbers payload** = IDOR uchun 1 dan N gacha
- Community da sekin → **ffuf** yoki **Turbo Intruder** ishlatish
- Status **302** = Ko'pincha muvaffaqiyatli login (redirect)
- Status **200** = Ko'pincha xato xabar (sahifa qaytadi)
