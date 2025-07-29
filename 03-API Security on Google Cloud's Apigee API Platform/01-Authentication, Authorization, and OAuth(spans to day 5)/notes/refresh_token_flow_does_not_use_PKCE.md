## 1️⃣ Initial Context

When the app first logged in using **Authorization Code + PKCE**, it got:

* **Access token** (short-lived, e.g., 1 hour)
* **Refresh token** (long-lived, e.g., days/weeks)

The refresh token is tied to the **same PKCE-bound authorization session** that created it.

---

## 2️⃣ Refresh Token Flow with PKCE

When the access token expires, the app doesn't repeat the PKCE challenge.
Instead, it exchanges the **refresh token** for a new access token.

---

### **Step-by-step**

1. **Access token expires.**

   * App detects a `401 Unauthorized` or checks token expiration time.

2. **App sends refresh request:**

   ```
   POST /token
     grant_type=refresh_token
     refresh_token=<refresh_token>
     client_id=<app_client_id>
   ```

   * No `code_verifier` is required because PKCE was only used during the initial authorization.
   * Public clients still don’t use a `client_secret`.

3. **Apigee (OAuth proxy) validates:**

   * Is the refresh token valid and not revoked?
   * Is it associated with this `client_id`?
   * Is it still within its lifetime?

4. **Tokens issued:**

   ```
   {
     "access_token": "new_access_123",
     "refresh_token": "refresh_token_123" (may be rotated),
     "expires_in": 3600
   }
   ```

5. **App replaces the expired access token.**

   * It may also store the new refresh token if token rotation is enabled.

---

## 3️⃣ Key Points

* **PKCE is only used during the initial authorization code exchange.**
  After that, the refresh token itself is proof of the client’s identity.
* If refresh token is stolen → the attacker can still get tokens (this is why refresh tokens must be stored securely).
* Some systems use **Refresh Token Rotation** (one-time-use refresh tokens) to reduce this risk.

---

✅ **Bottom line:**
PKCE protects the **initial token issuance**. After that, the refresh token becomes the secure credential for getting new tokens.

---

