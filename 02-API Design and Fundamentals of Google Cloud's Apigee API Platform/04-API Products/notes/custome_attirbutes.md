### 🧠 The Problem:

You’re building an API proxy for `/weather`, and you want **different apps to access it in different ways**:

* Some apps should be limited to **100 requests/day**
* Some apps should be allowed **faster responses**
* Others should get **premium data or features**

But your API proxy is the same for everyone — how can it treat apps differently?

---

### 💡 You Think:

"I don’t want to hardcode logic like:
`if appName == FreeApp then limit else...` — that would be messy and impossible to scale."

You need a smart way to **customize the API behavior per app**.

---

### 🚀 The Apigee Solution: Custom Attributes on API Products

Apigee says:
**"Why don’t you define custom attributes on your API products?"**

Here’s what you do:

1. You define **multiple API Products** — like `FreeWeather`, `PremiumWeather`
2. In each API product, you add **custom attributes**, like:

```json
"attributes": [
  { "name": "tier", "value": "free" },
  { "name": "rate_limit", "value": "100/day" }
]
```

Another product might have:

```json
"attributes": [
  { "name": "tier", "value": "premium" },
  { "name": "rate_limit", "value": "1000/day" }
]
```

---

### 🔐 Then You Tell Apps:

* “Hey FreeApp, you are subscribed to the `FreeWeather` product.”
* “Hey PremiumApp, you are subscribed to the `PremiumWeather` product.”

---

### 📦 At Runtime:

When the app sends a request with its **API key**, Apigee does the magic:

* It figures out **which product** the key is tied to
* It **automatically populates variables** like:

  * `product.attribute.tier`
  * `product.attribute.rate_limit`

---

### 🧠 Then In Your Proxy:

You write logic like:

```xml
<Condition>product.attribute.tier = "free"</Condition>
<Step>
  <Name>Quota-FreeTier</Name>
</Step>
```

Or:

```xml
<AssignVariable>
  <Name>quota.limit</Name>
  <Value>{product.attribute.rate_limit}</Value>
</AssignVariable>
```

---

### ✅ Summary:

You solved the problem **without hardcoding per-app logic**.
Just like how **target servers** allow you to change backend URLs based on environment without changing proxy code,
**custom attributes** allow you to change API behavior based on the API product — also without changing the proxy code.

This gives you full **flexibility**, **clean logic**, and **easy maintenance**.

