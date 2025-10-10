# Setting `zimbraPrefClientType` for Classic Web UI in Zimbra Open Source Edition

**Goal:** Configure the **Classic Web UI** (`zimbraPrefClientType = advanced`) for all existing and future users in Zimbra Open Source Edition (e.g., 8.8.x, 9.x, 10.x).

**Attribute:** `zimbraPrefClientType`
**Valid values:** `advanced`, `standard`, `modern`

* `advanced` = Classic UI

---

## 1️⃣ Set `zimbraPrefClientType` for All Existing Users

```bash
for u in $(zmprov -l gaa); do
    zmprov ma $u zimbraPrefClientType advanced
done
```

* Sets `zimbraPrefClientType` to **advanced** for all accounts.

**Restart (optional, if changes don’t apply):**

```bash
zmcontrol restart
```

---

## 2️⃣ Get `zimbraPrefClientType` for a User

```bash
zmprov ga <user>@mail.primary.test zimbraPrefClientType
```

* Replace `<user>` with the username (e.g., `amran`).
* **Expected output:**

  ```
  zimbraPrefClientType: advanced
  ```

---

## 3️⃣ Set `zimbraPrefClientType` for Future Users

**Set default COS:**

```bash
zmprov mc default zimbraPrefClientType advanced
```

**Verify COS:**

```bash
zmprov gc default zimbraPrefClientType
```

* **Expected output:**

  ```
  zimbraPrefClientType: advanced
  ```

---

## 4️⃣ Test

1. Log in at:

   ```
   https://mail.primary.test/zimbra
   ```
2. Confirm the **Classic (Ajax/HTML) UI** loads.
3. If the modern UI loads, check logs:

   ```bash
   tail -f /opt/zimbra/log/mailbox.log
   ```

---

## 5️⃣ Troubleshooting

* **Check Zimbra version:**

  ```bash
  zmcontrol -v
  ```
* Ensure `advanced` is supported (use `standard` if needed).
* **Verify attribute exists:**

  ```bash
  zmprov desc -a zimbraPrefClientType
  ```
* **Logs:** Check `/opt/zimbra/log/zmmailboxd.out` for errors.
* **Note:** Run all commands as the `zimbra` user:

  ```bash
  su - zimbra
  ```

---
