# 🛡️ Authentication and Session Persistence in Tauri + Vue Application

This document outlines the procedure for implementing a secure and seamless login experience in a Tauri-based desktop application using Vue. The goal is to keep the user within the application during login, persist their session securely, and maintain authenticated access until logout or manual cache clearance.

---

## ✅ Objectives

- Provide a native login UI inside the Tauri app.
- Send credentials securely to the backend API.
- Store session data locally and securely.
- Auto-login users if a valid session exists.
- Maintain session for authenticated API access.
- Log out and clear session when requested.

---

## 🖥️ 1. Render Native Login UI

- A Vue component is used to collect **email** and **password**.
- Includes a **"Remember Me"** checkbox.
- Captcha integration (optional for bot protection).

Example form:
```ts
<form @submit.prevent="handleLogin">
  <input type="email" v-model="email" required />
  <input type="password" v-model="password" required />
  <label>
    <input type="checkbox" v-model="rememberMe" />
    Remember Me
  </label>
  <button type="submit">Sign In</button>
</form>
```
---
## 📡 2. Send Credentials to Backend
On form submission:
```ts
import axios from "axios";

async function handleLogin() {{
  const response = await axios.post("https://api.circuitverse.org/users/sign_in.json", {{
    user: {{
      email,
      password
    }}
  }}, {{
    headers: {{
      "Content-Type": "application/json"
    }},
    withCredentials: true
  }});

  const token = response.data.token;
  if (rememberMe) {{
    await saveSession(email, token);
  }}
  router.push("/dashboard");
}}
```
## 🔐 3. Securely Store Session Data
### Option A: Using tauri-plugin-store
File-based JSON storage.

Setup:
```bash
cargo add tauri-plugin-store
```
In `tauri.conf.json`:

```json
{{
  "plugins": {{
    "store": {{
      "dir": ".store"
    }}
  }}
}}
```
Save session:
```js
import Store from "tauri-plugin-store-api";

const store = new Store(".store/session.json");
await store.set("email", email);
await store.set("token", token);
await store.save();
```
### Option B: Using tauri-plugin-secure-storage
OS-level secure storage.

Setup:
```bash
cargo add tauri-plugin-secure-storage
```
In `tauri.conf.json`:

```json
{{
  "plugins": {{
    "secure-storage": {{}}
  }}
}}
```
Save session:
```js
import {{ setSecure }} from "tauri-plugin-secure-storage-api";

await setSecure("email", email);
await setSecure("token", token);
```
## 🚀 4. Maintain Persistent Session on App Launch
In App.vue or main entry point:

```js
import Store from "tauri-plugin-store-api";
import {{ getSecure }} from "tauri-plugin-secure-storage-api";

async function autoLogin() {{
  const store = new Store(".store/session.json");
  const email = await store.get("email");
  const token = await store.get("token");

  // OR using secure storage:
  // const email = await getSecure("email");
  // const token = await getSecure("token");

  if (email && token) {{
    // Send the token to Authstore using the settoken(token) function of authstore
  }} else {{
    // otherwise everything happens normally
  }}
}}
```
🌐 5. Perform Authenticated API Actions
Use Axios for subsequent API requests:

```js
import axios from "axios";

const api = axios.create({{
  baseURL: "https://api.circuitverse.org",
  headers: {{
    "Content-Type": "application/json",
    "Authorization": `Bearer ${{storedToken}}`
  }},
  withCredentials: true
}});

// Example:
api.get("/api/v1/circuits").then(res => console.log(res.data));
```
## 🔁 6. "Remember Me" Behavior
In the login handler:

```js
if (rememberMe) {{
  await saveSession(email, token);
}}
```
🔓 7. Logout and Clear Cache
To log out and delete session:

```ts
import Store from "tauri-plugin-store-api";
import {{ removeSecure }} from "tauri-plugin-secure-storage-api";

async function logout() {{
  const store = new Store(".store/session.json");
  await store.clear();
  await store.save();

  await removeSecure("email");
  await removeSecure("token");

  router.push("/login");
}}
```
