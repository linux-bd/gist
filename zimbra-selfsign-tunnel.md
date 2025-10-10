# **Zimbra LAN Setup & Self-Signed Certificate – Full Summary**

## **1️⃣ Zimbra Server Setup (Ubuntu 22.04)**

1. Install Ubuntu 22.04 server
2. Set hostname properly:

```bash
sudo hostnamectl set-hostname mail.primary.test
```

3. Edit `/etc/hosts`:

```text
127.0.0.1   localhost
192.168.49.128   mail.primary.test
```

* **Important:** Only assign actual LAN IP to mail domain.
* `.test` domain fine for LAN testing.

4. Install **Zimbra Open Source Edition (OSE)** minimal packages:

* `zimbra-ldap` → Y
* `zimbra-logger` → Y
* `zimbra-mta` → Y
* `zimbra-store` → Y
* Skip optional components (`dnscache`, `snmp`, `license-daemon` if only mail).

5. Start Zimbra services:

```bash
sudo su - zimbra
zmcontrol status
```

---

## **2️⃣ Generate Self-Signed Certificate**

1. Switch to `zimbra` user:

```bash
sudo su - zimbra
```

2. Create a local CA:

```bash
/opt/zimbra/bin/zmcertmgr createca -new
```

3. Create server certificate with proper SAN:

```bash
/opt/zimbra/bin/zmcertmgr createcrt -new -subjectAltNames mail.primary.test
```

* **Important:** Browser will check CN/SAN → must match hostname you will access

4. Deploy certificate:

```bash
/opt/zimbra/bin/zmcertmgr deploycrt self
```

5. Restart services:

```bash
zmcontrol restart
```

6. Verify deployed certificate:

```bash
/opt/zimbra/bin/zmcertmgr viewdeployedcrt
```

---

## **3️⃣ Export Zimbra CA Certificate to Windows**

1. Copy CA certificate to a temporary folder:

```bash
cp /opt/zimbra/ssl/zimbra/ca/ca.pem /tmp/ca.pem
```

2. Transfer to Windows (via **Termius SCP**, **WinSCP**, or shared folder)

---

## **4️⃣ Import CA Certificate into Windows**

1. Optional: Rename `ca.pem` → `ca.crt`
2. Double-click file → Certificate window opens → **Install Certificate…**
3. Choose: **Local Machine** → Next
4. Store: **Trusted Root Certification Authorities** → Finish → Yes

* Browser now trusts this self-signed CA

---

## **5️⃣ Configure Windows Hosts File**

Edit `C:\Windows\System32\drivers\etc\hosts` **as Administrator**:

```text
192.168.49.128    mail.primary.test mail.secondary.test
127.0.0.1         localhost
```

* Flush DNS cache:

```cmd
ipconfig /flushdns
```

---

## **6️⃣ Access Zimbra in Browser**

* Always use **HTTPS** URL:

```
https://mail.primary.test         → Webmail
https://mail.primary.test:7071    → Admin Console
```

* **Important:**

  * Do NOT use `http://` → browser will show warning
  * Do NOT use `192.168.49.128` → CN mismatch → warning

* Optional: Add secondary SAN for `mail.secondary.test` if needed → create new certificate

---

## **7️⃣ Optional Notes**

* Self-signed certificate → trusted only on Windows where CA imported
* Other machines → same CA import needed
* LAN / private network testing → OK
* Production → use **Let’s Encrypt / valid public domain**

---

## **8️⃣ Zimbra Credentials 🔐📧**

```
zimadmin
0
```

---

### 🛠️ **Admin panel**

* 🌐 **URL:** [https://mail.primary.test:7071/zimbraAdmin/](https://mail.primary.test:7071/zimbraAdmin/)
* 👤 **User:** `admin@mail.primary.test`
* 🔑 **Pass:** `adminpass`

---

### 🧑‍💻 **User panel**

* 🌐 **URL:** [https://mail.primary.test/#1](https://mail.primary.test/#1)
* 👤 **User:** `admin@mail.primary.test`
* 🔑 **Pass:** `adminpass`

---

✅ **Result:**

* Zimbra services running
* Self-signed certificate deployed
* Windows trusts CA → browser shows **green padlock**
* Access via hostname `mail.primary.test` → no warnings
