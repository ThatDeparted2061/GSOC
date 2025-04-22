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

---
## 📡 2. Send Credentials to Backend
```html
import axios from "axios";

async function handleLogin() {
  const response = await axios.post("https://api.circuitverse.org/users/sign_in.json", {
    user: {
      email,
      password
    }
  }, {
    headers: {
      "Content-Type": "application/json"
    },
    withCredentials: true // include cookies if session-based auth
  });

  const token = response.data.token; // if Devise with JWT
  if (rememberMe) {
    await saveSession(email, token);
  }
  router.push("/dashboard");
}
