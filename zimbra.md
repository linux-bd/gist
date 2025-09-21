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
* Admin password → (choose strong password)  → `adminpass`

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

* Login: `admin@mail.primary.test`
* Password: `adminpass`

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
zmprov ca user1@mail.primary.test user1pass
```

### Change password

```bash
zmprov sp user1@mail.primary.test user1pass
```

### List all accounts

```bash
zmprov -l gaa
```

### Grant admin rights

```bash
zmprov ma user1@mail.primary.test zimbraIsAdminAccount TRUE
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

* Admin: `admin@mail.primary.test` `adminpass` → login at **[https://mail.primary.test:7071](https://mail.primary.test:7071)**
* Users: `user1@mail.primary.test` `user1pass` → login at **[https://mail.primary.test:8443](https://mail.primary.test:8443)**
* Multiple domains supported: `primary.test`, `secondary.test`, etc.
* Fully functional **mail server simulation** inside WSL2 + Docker.

## Logs
```

root@mail:/tmp/release# cat /etc/hosts
127.0.0.1       localhost
::1     localhost ip6-localhost ip6-loopback
fe00::  ip6-localnet
ff00::  ip6-mcastprefix
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
172.17.0.2      mail.primary.test mail

root@mail:/tmp/release# /opt/zimbra/libexec/zmsetup.pl
Operations logged to /tmp/zmsetup.20250921-162604.log
Installing LDAP configuration database...done.
Setting defaults...

DNS ERROR resolving MX for mail.primary.test
It is suggested that the domain name have an MX record configured in DNS
Change domain name? [Yes] no
done.
Checking for port conflicts

Main menu

   1) Common Configuration:
   2) zimbra-ldap:                             Enabled
   3) zimbra-logger:                           Enabled
   4) zimbra-mta:                              Enabled
   5) zimbra-snmp:                             Enabled
   6) zimbra-store:                            Enabled
        +Create Admin User:                    yes
        +Admin user to create:                 admin@mail.primary.test
******* +Admin Password                        UNSET
        +Anti-virus quarantine user:           virus-quarantine.zpbzc21gd@mail.primary.test
        +Enable automated spam training:       yes
        +Spam training user:                   spam.voaaikyv@mail.primary.test
        +Non-spam(Ham) training user:          ham.vehydfeqso@mail.primary.test
        +SMTP host:                            mail.primary.test
        +Web server HTTP port:                 8080
        +Web server HTTPS port:                8443
        +Web server mode:                      https
        +IMAP server port:                     7143
        +IMAP server SSL port:                 7993
        +POP server port:                      7110
        +POP server SSL port:                  7995
        +Use spell check server:               yes
        +Spell server URL:                     http://mail.primary.test:7780/aspell.php
        +Enable version update checks:         TRUE
        +Enable version update notifications:  TRUE
        +Version update notification email:    admin@mail.primary.test
        +Version update source email:          admin@mail.primary.test
        +Install mailstore (service webapp):   yes
        +Install UI (zimbra,zimbraAdmin webapps): yes

   7) zimbra-spell:                            Enabled
   8) zimbra-proxy:                            Enabled
   9) zimbra-imapd:                            Enabled
  10) Default Class of Service Configuration:
   s) Save config to file
   x) Expand menu
   q) Quit

Address unconfigured (**) items  (? - help) 6  [ and then 4 ]


Select, or 'r' for previous menu [r] r

Main menu

   1) Common Configuration:
   2) zimbra-ldap:                             Enabled
   3) zimbra-logger:                           Enabled
   4) zimbra-mta:                              Enabled
   5) zimbra-snmp:                             Enabled
   6) zimbra-store:                            Enabled
   7) zimbra-spell:                            Enabled
   8) zimbra-proxy:                            Enabled
   9) zimbra-imapd:                            Enabled
  10) Default Class of Service Configuration:
   s) Save config to file
   x) Expand menu
   q) Quit

*** CONFIGURATION COMPLETE - press 'a' to apply
Select from menu, or press 'a' to apply config (? - help) a
Save configuration data to a file? [Yes]
Save config in file: [/opt/zimbra/config.1107]
Saving config in /opt/zimbra/config.1107...done.
The system will be modified - continue? [No] yes
Operations logged to /tmp/zmsetup.20250921-162604.log
Setting local config values...done.
Initializing core config...Setting up CA...done.
Deploying CA to /opt/zimbra/conf/ca ...done.
Creating SSL zimbra-imapd certificate...done.
Creating new zimbra-store SSL certificate...done.
Creating new zimbra-ldap SSL certificate...done.
Creating new zimbra-mta SSL certificate...done.
Creating new zimbra-proxy SSL certificate...done.
Installing mailboxd SSL certificates...done.
Installing imapd SSL certificates...done.
Installing MTA SSL certificates...done.
Installing LDAP SSL certificate...done.
Installing Proxy SSL certificate...done.
Initializing ldap...done.
Setting replication password...done.
Setting Postfix password...done.
Setting amavis password...done.
Setting nginx password...done.
Setting BES searcher password...done.
Creating server entry for mail.primary.test...done.
Setting Zimbra IP Mode...done.
Saving CA in ldap...done.
Saving SSL Certificate in ldap...done.
Setting spell check URL...done.
Setting service ports on mail.primary.test...done.
Setting zimbraFeatureTasksEnabled=TRUE...done.
Setting zimbraFeatureBriefcasesEnabled=TRUE...done.
Checking current setting of zimbraReverseProxyAvailableLookupTargets
Querying LDAP for other mailstores
Searching LDAP for reverseProxyLookupTargets...done.
Adding mail.primary.test to zimbraReverseProxyAvailableLookupTargets
Setting TimeZone Preference...done.
Initializing mta config...done.
Setting services on mail.primary.test...done.
Adding mail.primary.test to zimbraMailHostPool in default COS...done.
Creating domain mail.primary.test...done.
Setting default domain name...done.
Creating domain mail.primary.test...already exists.
Creating admin account admin@mail.primary.test...done.
Creating root alias...done.
Creating postmaster alias...done.
Creating user spam.voaaikyv@mail.primary.test...done.
Creating user ham.vehydfeqso@mail.primary.test...done.
Creating user virus-quarantine.zpbzc21gd@mail.primary.test...done.
Setting spam training and Anti-virus quarantine accounts...done.
Initializing store sql database...done.
Setting zimbraSmtpHostname for mail.primary.test...done.
Configuring SNMP...done.
Setting up syslog.conf...done.
Enabling IMAP protocol for zimbra-imapd service...done.
Enabling IMAPS protocol for zimbra-imapd service...done.
Disabling zimbraNetworkModulesNGEnabled
Disabling zimbraNetworkMobileNGEnabled
Starting servers...done.
Installing common zimlets...
        com_zimbra_tooltip...done.
        com_zimbra_srchhighlighter...done.
        com_zimbra_cert_manager...done.
        com_zimbra_webex...done.
        com_zimbra_attachmail...done.
        com_zimbra_email...done.
        com_zimbra_clientuploader...done.
        com_zimbra_ymemoticons...done.
        com_zimbra_adminversioncheck...done.
        com_zimbra_viewmail...done.
        com_zimbra_attachcontacts...done.
        com_zimbra_mailarchive...done.
        com_zimbra_date...done.
        com_zimbra_url...done.
        com_zimbra_bulkprovision...done.
        com_zimbra_proxy_config...done.
        com_zimbra_phone...done.
Finished installing common zimlets.
Restarting mailboxd...done.
Creating galsync account for default domain...done.

You have the option of notifying Zimbra of your installation.
This helps us to track the uptake of the Zimbra Collaboration Server.
The only information that will be transmitted is:
        The VERSION of zcs installed (8.8.3_GA_1872_UBUNTU16_64)
        The ADMIN EMAIL ADDRESS created (admin@mail.primary.test)

Notify Zimbra of your installation? [Yes] no
Notification skipped
Setting up zimbra crontab...done.


Moving /tmp/zmsetup.20250921-162604.log to /opt/zimbra/log


Configuration complete - press return to exit


root@mail:/tmp/release# su - zimbra
zimbra@mail:~$ zmcontrol start
Host mail.primary.test
        Starting zmconfigd...Done.
        Starting logger...Done.
        Starting mailbox...Done.
        Starting memcached...Done.
        Starting proxy...Done.
        Starting amavis...Done.
        Starting antispam...Done.
        Starting antivirus...Done.
        Starting opendkim...Done.
        Starting snmp...Done.
        Starting spell...Done.
        Starting mta...Done.
        Starting stats...Done.
        Starting service webapp...Done.
        Starting zimbra webapp...Done.
        Starting zimbraAdmin webapp...Done.
        Starting zimlet webapp...Done.
        Starting imapd...Done.

zimbra@mail:~$ zmcontrol status
Host mail.primary.test
        amavis                  Running
        antispam                Running
        antivirus               Running
        imapd                   Running
        ldap                    Running
        logger                  Running
        mailbox                 Running
        memcached               Running
        mta                     Running
        opendkim                Running
        proxy                   Running
        service webapp          Running
        snmp                    Running
        spell                   Running
        stats                   Running
        zimbra webapp           Running
        zimbraAdmin webapp      Running
        zimlet webapp           Running
        zmconfigd               Running
zimbra@mail:~$
```
