## **Zimbra Login Autofill Tutorial Summary**

### **১. সমস্যা**

* Zimbra login page-এ username ও password input fields dynamic JSP variables দিয়ে তৈরি হয়।
* সরাসরি `login.jsp` edit করা নিরাপদ বা feasible নয়।
* আমরা চাই **default username & password auto-fill** করা হোক।

---

### **২. সমাধানের মূল কৌশল**

* **Client-side JavaScript injection**: browser load-এর সময় input field-এ value set করা।
* JSP / theme files untouched → future Zimbra update safe।

---

### **৩. স্টেপ-বাই-স্টেপ সমাধান**

#### **Step 1: custom JS তৈরি করা**

1. `/opt/zimbra/jetty/webapps/zimbra/js/customLogin.js` ফাইল তৈরি করুন।
2. ফাইলের মধ্যে লিখুন:

```javascript
window.addEventListener('load', function() {
    var userField = document.getElementById('username') || document.querySelector('input[name="username"]');
    if(userField) userField.value = 'amran@mail.primary.test';

    var passField = document.getElementById('password') || document.querySelector('input[name="password"]');
    if(passField) passField.value = 'nopass';
});
```

3. পারমিশন ঠিক করুন:

```bash
sudo chown zimbra:zimbra /opt/zimbra/jetty/webapps/zimbra/js/customLogin.js
sudo chmod 644 /opt/zimbra/jetty/webapps/zimbra/js/customLogin.js
```

---

#### **Step 2: Login page-এ JS include করা**

* `<head>` বা login template-এ JS include করুন:

```html
<script src="/js/customLogin.js"></script>
```

> যদি `login.jsp` edit না করা যায়, তখন:
>
> * Theme / header template inject করা যেতে পারে
> * বা Zimbra server-side file inject স্ক্রিপ্ট ব্যবহার করে auto-inject করা যায়

---

#### **Step 3: Zimbra restart করা**

```bash
su - zimbra
zmcontrol restart
```

---

### **৪. ফলাফল**

* Browser-এ login page load করলে:

  * Username = `amran@mail.primary.test`
  * Password = `nopass`
* JSP / core Zimbra files untouched → future update safe

---

### **৫. নিরাপত্তা নোট**

* শুধুমাত্র **test / local environment**-এ ব্যবহার করুন।
* Prodcution-এ এইভাবে password inject করা ঝুঁকিপূর্ণ।
* Rollback সহজ:

  * `/js/customLogin.js` মুছে দিন
  * Injected template / header ব্যাকআপ থেকে restore করুন
  * Zimbra restart করুন

---

### **৬. বিকল্প**

* Browser autofill / saved passwords ব্যবহার করা।
* Server-side modification না করেও একই effect পাওয়া সম্ভব।

---
