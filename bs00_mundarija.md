# 📚 Burp Suite — Mundarija

> **TryHackMe | Jr Pentester | Burp Suite bo'limi**
> Barcha roomlar bo'yicha shaxsiy konspekt va cheatsheet to'plami.

---

## 🗂️ Roomlar Ro'yxati

| # | Room | Asosiy Mavzu | Fayl |
|---|------|--------------|------|
| 01 | 🔧 The Basics | Proxy, Intercept, HTTP History, FoxyProxy | [bs01_the_basics.md](bs01_the_basics.md) |
| 02 | 🔁 Repeater | So'rov tahrirlash, yuborish, tarix | [bs02_repeater.md](bs02_repeater.md) |
| 03 | 🎯 Intruder | Sniper, Pitchfork, Cluster Bomb, Payloads | [bs03_intruder.md](bs03_intruder.md) |
| 04 | 🧰 Other Modules | Decoder, Comparer, Sequencer, Organizer | [bs04_other_modules.md](bs04_other_modules.md) |
| 05 | 🧩 Extensions | Turbo Intruder, JWT Editor, Autorize | [bs05_extensions.md](bs05_extensions.md) |

---

## ⚡ Tezkor Cheatsheet

### 🌐 Proxy Sozlash
```
Burp: Proxy → Options → 127.0.0.1:8080
Firefox: Settings → Network → Manual Proxy → 127.0.0.1:8080
HTTPS: http://burp → CA Certificate yuklab → Firefox ga import
FoxyProxy: Eng qulay usul — bir klikda yoqish/o'chirish
```

### ⌨️ Eng Muhim Klaviatura Yorliqlari
```
CTRL+R        → Repeater ga yuborish
CTRL+I        → Intruder ga yuborish
CTRL+Enter    → Repeater da yuborish (Send)
CTRL+U        → URL decode
CTRL+B        → Base64 decode
CTRL+H        → HTML decode
CTRL+Z        → Bekor qilish
```

### 🎯 Intruder Attack Turlari
```
Sniper        → 1 wordlist, 1 pozitsiya (username enum)
Battering Ram → 1 wordlist, barcha pozitsiya (user=pass)
Pitchfork     → Ko'p wordlist, parallel (juftlik ro'yxati)
Cluster Bomb  → Ko'p wordlist, barcha kombinatsiya (brute force)
```

### 🔓 Decoder — Tez Yo'riqnoma
```
Base64:   YWRtaW4=  →  admin
URL:      %3Cscript%3E  →  <script>
HTML:     &lt;script&gt;  →  <script>
Hex:      61 64 6d 69 6e  →  admin
Smart decode → Burp o'zi aniqlaydi
```

### 🧩 Muhim Extensions
```
Turbo Intruder  → Community da tez fuzzing
JWT Editor      → JWT tahlil va hujum
Autorize        → IDOR avtomatik tekshirish
Param Miner     → Yashirin parametrlar
DOM Invader     → DOM XSS topish
Logger++        → Kuchli trafik logi
```

---

## 🧠 Modullarni Eslab Qolish

| Modul | Qachon ishlatiladi |
|-------|-------------------|
| **Proxy** | Har doim — barcha trafik orqali o'tadi |
| **Repeater** | Zaiflikni qo'lda, takroran tekshirishda |
| **Intruder** | Brute force, fuzzing, enumeration |
| **Decoder** | Cookie, token, parametr decode qilishda |
| **Comparer** | Ikki javobdagi farqni topishda |
| **Sequencer** | Token tasodifiyligini o'lchashda |
| **Extensions** | Qo'shimcha funksiya kerak bo'lganda |

---

## 🔄 Tipik Pentest Oqimi (Burp bilan)

```
1. FoxyProxy yoqish → Burp Proxy orqali trafik yo'naltirish
        ↓
2. Saytni aylanib chiqish → HTTP History to'planadi
        ↓
3. Qiziqarli so'rovni topish → Repeater ga yuborish (CTRL+R)
        ↓
4. Qo'lda tekshirish (Repeater) → Zaiflik bormi?
        ↓
5. Avtomatlashtirish (Intruder) → Fuzz/Brute force
        ↓
6. Topilmalarni Decoder/Comparer bilan tahlil qilish
        ↓
7. Extensions yordamida chuqurroq tekshirish
```

---

*Yaratilgan: TryHackMe Jr Pentester — Burp Suite bo'limi asosida*
