# 🔐 **Zimbra 10.1.9 – “Forgot Password” (Account Recovery) Feature**

The **Forgot Password** feature allows users to securely reset their passwords via a verified recovery email — enabling **self-service password recovery** without admin intervention.

---

## 🧩 **Key Points**

| Feature                    | Description                                                                                                        |
| -------------------------- | ------------------------------------------------------------------------------------------------------------------ |
| 🆕 **Availability**        | Supported since **Zimbra 8.8.9**, fully functional in **10.1.9**                                                   |
| 🎯 **Purpose**             | Lets users reset passwords through a verified recovery email                                                       |
| 🌐 **Location**            | Appears as a **“Forgot Password?”** link on the login page:<br>`/opt/zimbra/jetty/webapps/zimbra/public/login.jsp` |
| 👁️ **Visibility Control** | Managed by the attribute:<br>`zimbraFeatureResetPasswordStatus` (enabled at **Domain** or **COS** level)           |

---

## ⚙️ **Setup Steps**

### 1️⃣ **Enable the Feature**

Run as the `zimbra` user:

```bash
# Domain-level
zmprov md <domain> zimbraFeatureResetPasswordStatus enabled

# COS-level
zmprov mc <cos_name> zimbraFeatureResetPasswordStatus enabled
```

✅ **Verify**:

```bash
zmprov gd <domain> | grep zimbraFeatureResetPasswordStatus
```

---

### 2️⃣ **Propagate & Restart**

```bash
zmprov fc -a all
zmcontrol restart
```

---

### 3️⃣ **User Requirement**

Each user must:

* Go to **Preferences → Accounts → Password Recovery Account Settings**
* Add and **verify** their recovery email address.

---

### 4️⃣ **Ensure Classic Client**

To make the link visible:

```bash
zmprov mcf zimbraWebClientDefaultToModern FALSE
```

Or use the **Classic Client URL**:

```
https://<domain>/zimbra?client=classic
```

🧭 *Modern client sometimes hides the link due to UI scaling issues.*

---

## 🧰 **Troubleshooting**

| Issue                      | Possible Fix                                                                                                  |
| -------------------------- | ------------------------------------------------------------------------------------------------------------- |
| 🔗 **Link Not Visible**    | Check virtual hostname:<br>`zmprov mcf zimbraVirtualHostname <domain>`                                        |
| 🎨 **Custom Skins**        | Review `/opt/zimbra/jetty/webapps/zimbra/skins/` for overrides                                                |
| 🧩 **JSP Include Missing** | Ensure this line exists:<br>`<c:if test="${domainInfo.attrs.zimbraFeatureResetPasswordStatus eq 'enabled'}">` |
| 📜 **Logs**                | Review `/opt/zimbra/log/mailbox.log` for related errors                                                       |

---

### 🧭 **Manual Temporary Fix**

> *(For testing only – not upgrade-safe)*

Add the following **after the `<label for="password">`** in
`/opt/zimbra/jetty/webapps/zimbra/public/login.jsp`:

```html
<a href="#" onclick="forgotPassword();" id="ZLoginForgotPassword" tabindex="7" aria-controls="ZLoginForgotPassword" aria-expanded="false">Forgot Password?</a>
```

---

## 📝 **Notes & Recommendations**

* ⚠️ **Avoid direct JSP edits** — they are overwritten after upgrades.
* 📧 Ensure each user sets and verifies a **recovery email** for functionality.
* 🌍 Test via the correct domain URL:

  ```
  https://mail.primary.test/zimbra
  ```

---

✨ **In Summary:**
The *Forgot Password* feature in **Zimbra 10.1.9 Open Source** empowers users with secure password recovery through verified emails — simple to enable, safe to use, and essential for modern self-service account management.

---
