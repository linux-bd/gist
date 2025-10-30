## 🖥️ প্যাকেজ ম্যানেজার অনুযায়ী ডিস্ট্রো তালিকা

| প্যাকেজ ম্যানেজার              | প্রধান ডিস্ট্রো                                                    | কভারেজ/শেয়ার (অনুমান) | লক্ষ্য ব্যবহার ক্ষেত্র                                     |
| ------------------------------ | ------------------------------------------------------------------ | --------------------- | ---------------------------------------------------------- |
| **APT / dpkg**                 | Ubuntu, Debian, Linux Mint, Pop!_OS, Elementary OS, Zorin OS, Kali | ~৫০%                  | ডেস্কটপ, সার্ভার, শিক্ষামূলক, সাধারণ ব্যবহার               |
| **DNF / YUM / RPM**            | Fedora, RHEL, CentOS Stream, AlmaLinux, Rocky Linux, Oracle Linux  | ~২৫%                  | এন্টারপ্রাইজ, সার্ভার, অফিস ও স্টেবল রিলিজ                 |
| **Pacman**                     | Arch Linux, Manjaro, EndeavourOS, ArcoLinux                        | ~৭%                   | Rolling release enthusiasts, power users                   |
| **APK**                        | Alpine Linux                                                       | ~৩%                   | Lightweight containers, embedded systems, security-focused |
| **Portage**                    | Gentoo, Calculate Linux, MocaccinoOS                               | <১%                   | Source-based customization, advanced users                 |
| **Slackware pkgtool/slackpkg** | Slackware, Zenwalk                                                 | <১%                   | Minimalist, legacy users                                   |
| **Zypper / RPM**               | openSUSE Leap/Tumbleweed, SLE                                      | ~৫%                   | Desktop & enterprise users (mainly Europe)                 |
| **Other independent**          | NixOS, Guix System, Void Linux (xbps)                              | <১%                   | Experimental, niche users                                  |

> **মোটামুটি কভারেজ:**
>
> * APT + RPM/DNF + Pacman + APK = ~৮৫‑৯০%+
> * বাকি ছোট/নিশ্চিত উৎসভিত্তিক distro খুব ছোট অংশ।

---

## 📊 কভারেজ চার্ট (অনুমানভিত্তিক)

```
APT/dpkg       ██████████████████████ 50%
DNF/RPM       ██████████ 25%
Pacman        ████ 7%
APK           ██ 3%
Zypper/RPM    ███ 5%
Portage       █ 1%
Slackware     █ 1%
Other         █ 1%
```

* চার্ট দেখাচ্ছে, APT/dpkg সবচেয়ে বেশি ব্যবহৃত।
* DNF/RPM বড় সার্ভার ও এন্টারপ্রাইজ সেগমেন্ট।
* Pacman ও APK বিশেষ niche, কিন্তু containers বা rolling release জন্য গুরুত্বপূর্ণ।
* Portage, Slackware, Other খুব ছোট শেয়ার, কেবল বিশেষ ব্যবহারকারীর জন্য।
