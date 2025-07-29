## 🔐 PKCE (Proof Key for Code Exchange)

* **What it is:**
  An extension to OAuth 2.0 defined in **RFC 7636**.
  It secures the **authorization code grant** for apps that **cannot store a client secret**, known as **public clients**.

---

### 1️⃣ The Problem

* Public clients (mobile apps, browser apps) cannot keep a `client_secret` safe.
* If an attacker steals the **authorization code**, they can directly exchange it for tokens.
* PKCE solves this by requiring proof that the same client that requested the authorization code is the one exchanging it for tokens.

---

### 2️⃣ How PKCE Works

1. **Code Verifier**

   * A random, high-entropy string created by the client at the start of the flow.
2. **Code Challenge**

   * A hashed version of the code verifier sent to the OAuth server during the authorization request.
3. **Binding**

   * The server stores the `code_challenge` alongside the authorization code.
4. **Token Exchange**

   * When exchanging the authorization code for tokens, the client sends the `code_verifier`.
   * The server hashes it and compares it to the stored `code_challenge`.
   * ✅ Match → token issued.
   * ❌ No match → request rejected.

---

### 3️⃣ Why It Works

* The `code_verifier` is never sent during the first request.
* A stolen `auth_code` is useless because only the legitimate client has the matching `code_verifier`.

---

### 4️⃣ Key Points

* **PKCE replaces the need for `client_secret`** in public clients.
* Works over HTTPS.
* Protects against **authorization code interception attacks**.
* Commonly used for mobile apps, SPAs, and any client without secure storage.

---

 Let’s explain the **Authorization Code Flow with PKCE** from the **end-user perspective** and how Apigee handles everything behind the scenes.

---

## 1️⃣ End-user use case

**Scenario:**

* User opens a mobile app (public client) and wants to log in using their account.
* The app uses Apigee as a gateway and OAuth with PKCE for secure authentication.

---

## 2️⃣ Step-by-step flow

### **Step 1: App prepares PKCE values**

* The app generates:

  ```
  code_verifier = "p9TgKx7Q..."
  code_challenge = hash(code_verifier) → "h8D2eL3z..."
  ```
* The `code_verifier` stays in the app's memory.
* The `code_challenge` is sent to Apigee.

---

### **Step 2: App redirects user to login**

* App opens browser or web view:

  ```
  GET https://apigee-proxy/authorize
    ?client_id=mobile123
    &response_type=code
    &redirect_uri=myapp://callback
    &code_challenge=h8D2eL3z...
    &code_challenge_method=S256
  ```

**What Apigee does:**

1. Stores `code_challenge` temporarily.
2. Validates `client_id` and `redirect_uri`.
3. Redirects the user’s browser to the **authorization server login page**.

---

### **Step 3: User authenticates**

* User sees the login page (hosted by the authorization server).
* User enters credentials.
* Authorization server asks for consent (if required).
* Authorization server tells Apigee: ✅ User authenticated.

---

### **Step 4: Apigee issues auth code**

* Apigee generates:

  ```
  auth_code = "auth123"
  ```
* Apigee associates it with:

  ```
  auth123 → code_challenge=h8D2eL3z..., client_id=mobile123
  ```
* Apigee redirects the user’s browser back to the app:

  ```
  myapp://callback?code=auth123
  ```

---

### **Step 5: App exchanges auth code for tokens**

The app calls Apigee:

```
POST https://apigee-proxy/token
  grant_type=authorization_code
  code=auth123
  redirect_uri=myapp://callback
  code_verifier=p9TgKx7Q...
```

**What Apigee does:**

1. Looks up `auth123` and retrieves stored `code_challenge`.
2. Hashes `code_verifier`.
3. Compares it to `code_challenge`.

   * ✅ If match → Issues access token and refresh token.
   * ❌ If mismatch → Rejects request.

---

### **Step 6: App gets tokens**

Apigee responds:

```
{
  "access_token": "abc123",
  "refresh_token": "xyz789",
  "expires_in": 3600
}
```

The app now uses the `access_token` to call APIs through Apigee.

---

### **Step 7: Cleanup**

* Apigee deletes the `auth_code` and its `code_challenge`.
* The app keeps the `refresh_token` (to get new access tokens later).

---

✅ **From the end-user perspective:**
They just "logged in with their account."
Behind the scenes, PKCE protected the flow so no stolen auth code could be used by an attacker.

---




