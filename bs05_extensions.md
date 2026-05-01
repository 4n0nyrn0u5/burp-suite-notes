# 🧩 Burp Suite: Extensions (Kengaytmalar)

> **Maqsad:** Burp Suite ning funksionalligini BApp Store va custom extension lar orqali kengaytirish.

---

## 📖 Extensions nima?

**Extensions** — Burp Suite ga qo'shimcha funksiyalar qo'shadigan plaginlar. Java, Python yoki Ruby da yoziladi. BApp Store da 300+ tayyor extension bor.

---

## 🏪 BApp Store (Extension Do'koni)

```
Extensions → BApp Store

# O'rnatish:
1. Extension nomini qidiring
2. "Install" bosing
3. Installed tab da ko'rish mumkin

# Bepul vs Pullik:
Bepul     → Community va Pro da ishlaydi
Pro only  → Faqat Pro versiyada
```

---

## ⭐ Eng Muhim Extensions

### 1. 🔥 Turbo Intruder
**Community da Intruder sekin — Turbo Intruder tez!**

```python
# O'rnatish: BApp Store → "Turbo Intruder" → Install

# Ishlatish:
So'rov ustida O'ng klik → "Send to Turbo Intruder"

# Standart skript (brute force):
def queueRequests(target, wordlists):
    engine = RequestEngine(
        endpoint=target.endpoint,
        concurrentConnections=5,
        requestsPerConnection=100,
        pipeline=False
    )
    for word in open('/usr/share/wordlists/rockyou.txt'):
        engine.queue(target.req, word.rstrip())

def handleResponse(req, interesting):
    table.add(req)

# Race Condition uchun skript:
def queueRequests(target, wordlists):
    engine = RequestEngine(
        endpoint=target.endpoint,
        concurrentConnections=30,
        engine=Engine.BURP2
    )
    for i in range(30):
        engine.queue(target.req, gate='race1')
    engine.openGate('race1')   # Hammasi bir vaqtda!

def handleResponse(req, interesting):
    table.add(req)
```

### 2. 🔍 Active Scan++
Pro versiya uchun — skanerni kuchaytiradi.

### 3. 🌐 Logger++
Oddiy Logger dan kuchli — filter, grep, real-time monitoring.

```
# O'rnatish: BApp Store → Logger++ → Install
# Ishlatish:
Logger++ tab → Barcha trafik
"Grep" funksiyasi → Matн qidirish
"Save" → CSV ga eksport
```

### 4. 🎭 CSRF Scanner
CSRF zaifliklarini avtomatik topadi.

### 5. 🔐 JWT Editor
JWT tokenlarni tahlil qilish, o'zgartirish va hujum qilish.

```
# O'rnatish: BApp Store → "JWT Editor" → Install

# JWT ni tahrirlash:
1. So'rovdagi JWT ni Repeater da toping
2. "JSON Web Token" tabini bosing (pastda)
3. Header va Payload ni o'zgartiring
4. "Sign" → yangi kalit bilan imzolash
5. Yuboring → Server qabul qiladimi?

# Mashhur hujumlar:
- alg: none → imzosiz token
- RS256 → HS256 → public key bilan imzolash
- Weak secret → brute force
```

### 6. 🕷️ Param Miner
Yashirin HTTP parametrlarini topadi.

```
# O'rnatish: BApp Store → "Param Miner" → Install

# Ishlatish:
So'rov ustida O'ng klik → "Guess params"
→ Burp yashirin parametrlarni qidiradi

# Topishi mumkin:
?debug=true
?admin=true
?test=1
X-Custom-Header: value
```

### 7. 🔎 Retire.js
Eski va zaif JavaScript kutubxonalarini aniqlaydi.

```
# O'rnatish: BApp Store → "Retire.js" → Install
# Avtomatik ishlaydi — Dashboard da natijalar chiqadi
```

### 8. 🛡️ Hackvertor
Murakkab encoding/decoding va payload transformatsiyasi.

```
# Payload ustida O'ng klik → "Hackvertor"
# Turli kodlash usullarini birlashtirish:
<@base64><@url_encode>alert(1)<@/url_encode><@/base64>
```

### 9. 📊 Autorize
Avtomatik avtorizatsiya tekshiradi — IDOR va BOLA topish uchun.

```
# O'rnatish: BApp Store → "Autorize" → Install

# Ishlatish:
1. Autorize tab → Kam ruxsatli foydalanuvchi cookie si kiriting
2. Yuqori ruxsatli foydalanuvchi sifatida saytni aylanib chiqing
3. Autorize har so'rovni kam ruxsatli cookie bilan qayta yuboradi
4. Natija:
   Qizil   = Ruxsatsiz kirish mumkin (IDOR/BOLA!)
   Yashil  = To'g'ri bloklangan
   Sariq   = Noaniq
```

### 10. 🌍 DOM Invader
DOM-Based XSS va prototype pollution topish.

```
# Burp Suite browser da mavjud (o'rnatish shart emas)
# Yoki: BApp Store → "DOM Invader"

# Ishlatish:
Burp Browser → F12 → DOM Invader (extension)
→ "Canary" yoqing → Saytni aylanib chiqing
→ DOM Invader source/sink ni avtomatik topadi
```

---

## 🔧 Custom Extension Yozish

### Python bilan (Jython kerak):

```python
# Burp Suite → Extensions → Options → Python Environment
# Jython standalone JAR o'rnating

# Oddiy extension misoli (custom_ext.py):
from burp import IBurpExtender, IHttpListener

class BurpExtender(IBurpExtender, IHttpListener):
    def registerExtenderCallbacks(self, callbacks):
        self._callbacks = callbacks
        self._helpers = callbacks.getHelpers()
        callbacks.setExtensionName("My Custom Extension")
        callbacks.registerHttpListener(self)
        print("Extension yuklandi!")
    
    def processHttpMessage(self, toolFlag, messageIsRequest, messageInfo):
        if messageIsRequest:
            request = messageInfo.getRequest()
            # Har so'rovni bu yerda qayta ishlash
            print("So'rov ushlanди!")
```

---

## ⚙️ Extensions Sozlamalari

```
Extensions → Installed tab:
- ✅ belgi = Yoqilgan
- ☐ belgi = O'chirilgan (o'chirmasdan saqlash)
- "Remove" = O'chirish
- "Up/Down" = Tartibni o'zgartirish (bajarilish ketma-ketligi)

Extensions → Options:
- Java: JDK o'rnatilishi shart
- Python: Jython JAR kerak
- Ruby: JRuby kerak
```

---

## 🎯 Amaliy Misol: JWT Editor bilan Hujum

```
# Vaziyat: JWT autentifikatsiyasi zaif

# 1-qadam: JWT ni toping
Authorization: Bearer eyJhbGciOiJIUzI1NiJ9.eyJ1c2VyIjoiam9obiJ9.xxx

# 2-qadam: Repeater ga yuboring
# Pastdagi "JSON Web Token" tabini bosing

# 3-qadam: Payload ni ko'ring:
{
  "user": "john",
  "role": "user"
}

# 4-qadam: O'zgartiring:
{
  "user": "admin",
  "role": "admin"
}

# 5-qadam: alg: none hujumi:
Header: {"alg": "none"}
→ "Sign" → "Don't sign"
→ Yuboring → Admin sifatida kirganmisiz?

# 6-qadam: Weak secret brute force:
JWT Editor → "JSON Web Token" → "Attack" → "Embedded JWK"
yoki hashcat:
hashcat -a 0 -m 16500 jwt.txt /usr/share/wordlists/rockyou.txt
```

---

## 💡 Eslab Qolish Uchun

| Extension | Vazifasi | Muhimligi |
|-----------|---------|-----------|
| **Turbo Intruder** | Tez fuzzing | ⭐⭐⭐⭐⭐ |
| **JWT Editor** | JWT hujumlari | ⭐⭐⭐⭐⭐ |
| **Autorize** | IDOR avtomatik | ⭐⭐⭐⭐⭐ |
| **Param Miner** | Yashirin parametr | ⭐⭐⭐⭐ |
| **Logger++** | Kuchli logging | ⭐⭐⭐⭐ |
| **DOM Invader** | DOM XSS | ⭐⭐⭐⭐ |
| **Retire.js** | Zaif JS kutubxona | ⭐⭐⭐ |
| **Hackvertor** | Murakkab encoding | ⭐⭐⭐ |

**Eng muhim eslatmalar:**
- Community da Intruder sekin → **Turbo Intruder** o'rnating
- JWT tekshirishda → **JWT Editor** shart
- IDOR avtomatik topish → **Autorize**
- Yashirin parametr → **Param Miner**
- BApp Store → Extensions → Install → Tayyor!
