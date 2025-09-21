# 📖 Full Zimbra Setup on WSL2 (Ubuntu 24.04 + Docker)

---

## 1. Install Requirements

On **Windows**:

1. Install **Docker Desktop** with **WSL2 backend enabled**.
2. Enable integration with your Ubuntu 24.04 distro in Docker Desktop.

On **Ubuntu WSL2**:

```bash
sudo apt update && sudo apt install -y docker.io docker-compose
```

Make sure Docker is running:

```bash
docker ps
```

---

## 2. Configure Networking & DNS

Since Zimbra requires valid DNS and hostnames:

1. Edit **Windows hosts file** (`C:\Windows\System32\drivers\etc\hosts`):

   ```
   127.0.0.1    mail.primary.test
   127.0.0.1    mail.secondary.test
   ```

2. Also update inside WSL2 (`/etc/hosts`):

   ```bash
   sudo nano /etc/hosts
   ```

   Add:

   ```
   127.0.0.1       localhost
   ::1             localhost ip6-localhost
   172.19.0.2      mail.primary.test mail
   172.19.0.3      mail.secondary.test
   ```

*(⚠ Replace `172.19.x.x` with your container’s IP, or let Docker handle it with port mapping — see step 3.)*

---

## 3. Run Zimbra Docker Container

Start the container with **port mappings** so your browser can access Zimbra:

```bash
docker run -it --name zimbra --hostname mail.primary.test \
  -p 25:25 -p 80:80 -p 443:443 \
  -p 110:110 -p 995:995 \
  -p 143:143 -p 993:993 \
  -p 465:465 -p 587:587 \
  -p 7071:7071 -p 8443:8443 \
  zimbra/zcs-foss:latest bash
```

This exposes:

* SMTP → 25, 465, 587
* IMAP/POP → 143, 993, 110, 995
* Webmail → 8443
* Admin console → 7071

---

## 4. Run Initial Zimbra Installer

Inside container:

```bash
/opt/zimbra/libexec/zmsetup.pl
```

* Hostname → `mail.primary.test`
* Domain → `primary.test`
* Admin user → `admin@primary.test`
* Admin password → (choose strong password)

**Important**: Don’t type `yes` — that creates domain `yes` (like in your test). Always enter `primary.test`.

---

## 5. Start Services

After installer completes:

```bash
su - zimbra
zmcontrol start
zmcontrol status
```

All services (ldap, mailbox, mta, proxy, etc.) should say **Running**.

If LDAP or MySQL is down, fix with:

```bash
ldap start
zmcontrol restart
```

---

## 6. Access Admin Panel

From Windows browser:

```
https://mail.primary.test:7071/zimbraAdmin/
```

* Login: `admin@primary.test`
* Password: the one you set during setup

---

## 7. Access Webmail

For normal users:

```
https://mail.primary.test:8443/
```

Login with user accounts you create.

---

## 8. Manage Domains & Users (Command Line)

Inside container (as `zimbra` user):

### Create a domain

```bash
zmprov cd secondary.test
```

### Create a user

```bash
zmprov ca user1@primary.test user1pass
```

### Change password

```bash
zmprov sp user1@primary.test newpass123
```

### List all accounts

```bash
zmprov -l gaa
```

### Grant admin rights

```bash
zmprov ma user1@primary.test zimbraIsAdminAccount TRUE
```

---

## 9. Troubleshooting

* If services don’t start:

  ```bash
  zmcontrol status
  tail -f /opt/zimbra/log/mailbox.log
  ```

* If hostname/DNS issues:

  ```bash
  hostname -f
  cat /etc/hosts
  ```

* If stuck with wrong domain (`yes`):

  * Delete with:

    ```bash
    zmprov dd yes
    ```
  * Reinstall container for a clean config.

---

## ✅ End Result

* Admin: `admin@primary.test` → login at **[https://mail.primary.test:7071](https://mail.primary.test:7071)**
* Users: `user1@primary.test` → login at **[https://mail.primary.test:8443](https://mail.primary.test:8443)**
* Multiple domains supported: `primary.test`, `secondary.test`, etc.
* Fully functional **mail server simulation** inside WSL2 + Docker.
