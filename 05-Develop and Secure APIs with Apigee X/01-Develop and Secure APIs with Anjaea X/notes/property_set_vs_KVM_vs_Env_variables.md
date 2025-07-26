## 🧩 What is a Property Set in Apigee?

A **Property Set** in **Apigee** is a **key-value storage** that acts like a **persistent configuration store** for your API proxies.

You can think of it as:

> "A mini-database for storing configuration or shared data that your API policies or proxies can read and write."

---

## 🔐 Key Features

* Stores **key-value pairs**
* Shared across **all environments** within an Apigee organization
* Can be **read or written** using policies (like JavaScript, Java, etc.)
* Good for **small configuration data**, not large or sensitive data

---

## 📦 Common Use Cases

1. **Feature Flags**
   Enable/disable features dynamically without changing code.

2. **Rate Limiting Configs**
   Store and adjust quotas or limits.

3. **Routing Info**
   Dynamic backend URLs or server switches.

4. **Shared Secret Keys or Tokens**
   For testing (but *not recommended* for production secrets).

---

## 🛠️ How to Work with Property Sets

You typically interact with Property Sets using **JavaScript** or **Java policies** in Apigee.

### ✅ Example: Setting a Property

```javascript
// JavaScript Policy
var properties = context.getVariable("organization.properties");
properties["maintenance_mode"] = "true";
```

### ✅ Example: Getting a Property

```javascript
var maintenance = context.getVariable("organization.properties.maintenance_mode");
if (maintenance === "true") {
    // Return a 503 response
}
```

---

## ⚠️ Important Notes

* Property sets are **not environment-scoped**. Changes affect **all environments** in your Apigee org.
* Do **not** store sensitive information like passwords or API secrets in property sets.
* For secure data, use **KVMs (Key-Value Maps)** with encryption.

---

## 🔄 Property Set vs. KVM vs. Environment Variables

| Feature               | Property Set | KVM (Key Value Map)           | Environment Variable |
| --------------------- | ------------ | ----------------------------- | -------------------- |
| Scope                 | Org-wide     | Environment-scoped (optional) | Environment-scoped   |
| Persistent Storage    | ✅            | ✅                             | ❌ (read-only)        |
| Modifiable at Runtime | ✅            | ✅                             | ❌                    |
| Encrypted Option      | ❌            | ✅ (KVM can be encrypted)      | ❌                    |

---

## ✅ Summary

* A **Property Set** is a key-value configuration store for Apigee API proxies.
* Useful for **dynamic configuration** without redeploying APIs.
* Avoid storing secrets; use **KVM** for sensitive data.

---
