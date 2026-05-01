# 🧰 Burp Suite: Other Modules (Boshqa Modullar)

> **Maqsad:** Burp Suite ning kam ma'lum lekin juda foydali modullari — Decoder, Comparer, Sequencer va Organizer bilan ishlash.

---

## 🔓 1. Decoder (Kodlovchi/Dekodlovchi)

### Decoder nima?
Ma'lumotlarni turli formatlarda **encode** (kodlash) va **decode** (dekodlash) qilish vositasi. Pentesting da ma'lumotlar ko'pincha kodlangan holda uzatiladi.

### Qo'llab-quvvatlanadigan formatlar:

| Format | Misol (input) | Natija |
|--------|---------------|--------|
| **URL** | `hello world` | `hello+world` |
| **HTML** | `<script>` | `&lt;script&gt;` |
| **Base64** | `admin` | `YWRtaW4=` |
| **ASCII Hex** | `A` | `41` |
| **Hex** | `41 64 6d 69 6e` | `Admin` |
| **Octal** | `101` | `A` |
| **Binary** | `01000001` | `A` |
| **Gzip** | Compressed data | O'qilishi mumkin matn |

### Ishlatish:
```
Decoder tabiga o'ting → Matn kiriting yoki paste qiling

# Decode qilish:
"Decode as" → Format tanlang (Base64, URL, HTML...)

# Encode qilish:
"Encode as" → Format tanlang

# Zanjirli (bir nechta):
Base64 decode → keyin URL decode
(Ko'p qatlamli kodlashni yechish uchun)

# Smart decode:
"Smart decode" tugmasi → Burp o'zi taxmin qiladi
```

### Amaliy misollar:
```bash
# Cookie ni decode qilish:
dXNlcjphZG1pbg==  →  Base64 decode  →  user:admin

# URL encoded parametr:
%3Cscript%3E  →  URL decode  →  <script>

# JWT token (3 qism, . bilan ajratilgan):
eyJhbGciOiJIUzI1NiJ9  →  Base64 decode  →  {"alg":"HS256"}

# Hex dan matn:
48 65 6c 6c 6f  →  ASCII Hex decode  →  Hello
```

---

## ⚖️ 2. Comparer (Solishtiruvchi)

### Comparer nima?
Ikki so'rov yoki javobni **farqlarini ajratib** ko'rsatadi. Ko'zga ko'rinmas o'zgarishlarni topishda foydali.

### Qachon ishlatiladi?
- Admin va oddiy foydalanuvchi javoblarini solishtirish
- To'g'ri va noto'g'ri parol javoblarini solishtirish
- IDOR topishda — ikki foydalanuvchi ma'lumotlarini solishtirish
- Filtrlangan va filtirlanmagan so'rovlarni solishtirish

### Ishlatish:
```
# Usul 1: Proxy/Repeater dan
So'rov/Javob ustida O'ng klik → "Send to Comparer"
(Ikki xil so'rovni yuboring)

# Usul 2: Qo'lda
Comparer → "Paste" yoki "Load" (fayldan)

# Solishtirish:
"Words" → So'z darajasida farq
"Bytes" → Bayt darajasida farq (aniqroq)

# Natija:
Yashil = Bir xil
Qizil/sariq = Farqli joy
```

### Amaliy misol:
```
# Admin va oddiy foydalanuvchi javobini solishtirish:
1. Oddiy foydalanuvchi sifatida /dashboard ga kiring → javobni Comparer ga yuboring
2. Cookie → role=admin ga o'zgartiring → javobni Comparer ga yuboring
3. Comparer → Words → Farqni ko'ring
# Agar "Admin Panel" kabi matn chiqsa → cookie manipulyatsiya ishladi!
```

---

## 🎲 3. Sequencer (Ketma-ketlik Tekshiruv)

### Sequencer nima?
Token, session ID, CSRF token kabi **tasodifiy (random) qiymatlarning sifatini** o'lchaydi. Agar token bashorat qilinishi mumkin bo'lsa → xavfli!

### Qachon ishlatiladi?
- Session ID qanchalik tasodifiy?
- CSRF token bashorat qilinishi mumkinmi?
- OTP (bir martalik kod) zaifligi bormi?

### Ishlatish:
```
# 1. Token generatsiya qiladigan so'rovni toping
# (Masalan: /login → Set-Cookie: session=XXX)

# 2. So'rovni Sequencer ga yuboring:
O'ng klik → "Send to Sequencer"

# 3. Token joylashgan headerini belgilang:
"Token Location within response" → Cookie: session=[bu qism]

# 4. "Start live capture" bosing
# Burp 100+ token yig'adi va tahlil qiladi

# 5. "Analyze now" → Natija:
- Entropy (tasodifiylik darajasi): bits
- 128 bit+ = Yaxshi
- 32 bit- = Xavfli, bashorat qilinishi mumkin!
```

---

## 📁 4. Organizer (Tartiblovchi)

### Organizer nima?
Muhim so'rovlarni **saqlash va izoh qo'shish** imkonini beradi. Uzoq pentest davomida topilmalarni tartiblab borish uchun.

```
# So'rovni Organizer ga yuborish:
O'ng klik → "Send to Organizer"

# Organizer da:
- Har so'rovga izoh yozing
- Rang bering (qizil = critical, sariq = medium)
- Guruhlab saqlang
- Eksport qiling (hisobot uchun)
```

---

## 🌐 5. Logger (Jurnalchi)

### Logger nima?
Barcha HTTP trafigini saqlaydi — Proxy History dan farqi: Extensions trafigini ham yozadi.

```
Logger tab → Barcha so'rovlar ko'rinadi
Filter: Extension, Method, Status code bo'yicha filtrlash
```

---

## 🕵️ 6. Target Analyzer

```
Target → Site Map → So'rov ustida O'ng klik → "Engagement tools"

# Mavjud funksiyalar:
- Find comments     → HTML izohlarini topish
- Find scripts      → JS fayllarini topish
- Find references   → Barcha havolalarni topish
- Discover content  → Yashirin sahifalarni topish
- Schedule task     → Vazifani rejalashtirish
```

---

## 🎯 Amaliy Misol: Decoder + Comparer birgalikda

```
# Vaziyat: Cookie manipulyatsiya tekshirish

# 1-qadam: Cookie ni Decoder da ochish
Cookie: user=dXNlcjoxMDAx
→ Base64 decode → user:1001

# 2-qadam: O'zgartirish
user:1001 → user:1 ga o'zgartir
→ Base64 encode → dXNlcjox

# 3-qadam: Yangi cookie bilan so'rov yuboring (Repeater)
Cookie: user=dXNlcjox
→ Javob: Admin ma'lumotlari!

# 4-qadam: Comparer da solishtirish
Asl javob vs yangi javob → Qanday farq bor?
```

---

## 💡 Eslab Qolish Uchun

| Modul | Vazifasi | Qachon |
|-------|---------|--------|
| **Decoder** | Encode/Decode | Cookie, token, parametr tahlili |
| **Comparer** | Ikki javobni solishtirish | IDOR, admin bypass tekshirish |
| **Sequencer** | Token tasodifiyligini o'lchash | Session, CSRF zaiflik |
| **Organizer** | Topilmalarni saqlash | Uzoq pentest jarayoni |
| **Logger** | Barcha trafik | Extension so'rovlarini ko'rish |

**Tezkor eslatma:**
- **Base64** `=` bilan tugasa → `YWRtaW4=` kabi
- **URL encode** `%` bilan boshlanadi → `%3C%3E`
- **Comparer** → Words (tez) yoki Bytes (aniq)
- **Sequencer** → 128 bit+ entropy = xavfsiz token
- `Smart decode` → Burp o'zi aniqlaydi (eng qulay)
