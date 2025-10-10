## Zimbra Login Banner পরিবর্তনের সংক্ষিপ্ত গাইড

### 1️⃣ ব্যাকআপ তৈরি

প্রথমে মূল ব্যানার ফাইলের ব্যাকআপ নিন:

```bash
cd /opt/zimbra/jetty/webapps/zimbra/skins/_base/logos/
cp LoginBanner.png LoginBanner.png.bak
cp TouchLoginBanner.png TouchLoginBanner.png.bak
cp TouchLoginBanner@2x.png TouchLoginBanner@2x.png.bak
```

---

### 2️⃣ নতুন ব্যানার তৈরি

* **ডেস্কটপ:** `LoginBanner.png`

  * আকার: 260px × 34px (CSS অনুযায়ী)
* **মোবাইল:** `TouchLoginBanner.png`

  * High-DPI মোবাইলের জন্য: `TouchLoginBanner@2x.png`

💡 সব ব্যানারের ফাইল ফরম্যাট PNG রাখতে হবে।

---

### 3️⃣ নতুন ব্যানার আপলোড

সার্ভারে নতুন ব্যানার কপি করুন:

```bash
cp /path/to/new/LoginBanner.png /opt/zimbra/jetty/webapps/zimbra/skins/_base/logos/LoginBanner.png
cp /path/to/new/TouchLoginBanner.png /opt/zimbra/jetty/webapps/zimbra/skins/_base/logos/TouchLoginBanner.png
cp /path/to/new/TouchLoginBanner@2x.png /opt/zimbra/jetty/webapps/zimbra/skins/_base/logos/TouchLoginBanner@2x.png
```

---

### 4️⃣ Zimbra সার্ভার রিস্টার্ট

পরিবর্তন দেখানোর জন্য Zimbra রিস্টার্ট করুন:

```bash
sudo su - zimbra
zmcontrol restart
```

---

### 5️⃣ ব্রাউজার ক্যাশ ক্লিয়ার

নতুন ব্যানার দেখতে ব্রাউজারে **hard refresh** করুন:

* Windows: `Ctrl + Shift + R`
* Firefox: `Ctrl + F5`

---

✅ **নোটস:**

* ডেস্কটপ ব্যানার: `LoginBanner.png`
* মোবাইল/ট্যাবলেট: `TouchLoginBanner.png`
* High-DPI মোবাইল: `TouchLoginBanner@2x.png`
* ব্যানারের সাইজ পরিবর্তন করলে `.ImgLoginBanner` CSS-এ `width` ও `height` পরিবর্তন করতে হবে।

---
