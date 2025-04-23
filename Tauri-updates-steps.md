# **Procedure for Implementing Updater in Tauri App and Signing of Releases**

## **1. Installing the Updater Plugin**  
Use the package manager to install the Tauri updater plugin:

```sh
npm run tauri add updater
```

## **2. Generating Signing Keys**  
Generate a key pair for signing updates:

```sh
npm run tauri signer generate -- -w ~/.tauri/myapp.key
```

Securely store the **private key**; it is required for signing update files.

## **3. Configuring Updater in `tauri.conf.json`**  
Enable update artifacts generation and disable automatic updates:

```json
{
  "bundle": {
    "createUpdaterArtifacts": true
  },
  "plugins": {
    "updater": {
      "pubkey": "CONTENT FROM PUBLICKEY.PEM",
      "endpoints": [
        "https://github.com/user/repo/releases/latest/download/latest.json"
      ],
      "dialog": false
    }
  }
}
```

On **Windows**, optionally configure `installMode`:

```json
{
  "plugins": {
    "updater": {
      "windows": {
        "installMode": "passive"
      }
    }
  }
}
```

## **4. Building Update Artifacts**  
Set the private key environment variable (required for signing updates):

```sh
export TAURI_SIGNING_PRIVATE_KEY=" Path or content of our private key "
export TAURI_SIGNING_PRIVATE_KEY_PASSWORD=" likewise "
```

Run Tauri build:

```sh
npm run tauri build
```

The update artifacts will be generated based on the OS:
- **Linux**: `.AppImage` and `.sig`
- **MacOS**: `.tar.gz` and `.sig`
- **Windows**: `.msi`, `.exe`, and `.sig`

## **5. Hosting Updates**  
### **Static JSON File (GitHub Releases / S3 / CDN)**  
Create a `latest.json` file with update details:

```json
{
  "version": "1.0.1",
  "notes": "Bug fixes and performance improvements",
  "pub_date": "2025-03-29T12:00:00Z",
  "platforms": {
    "windows-x86_64": {
      "signature": "SIGNATURE_STRING",
      "url": "https://github.com/user/repo/releases/download/v1.0.1/myapp-setup.exe"
    },
    "linux-x86_64": {
      "signature": "SIGNATURE_STRING",
      "url": "https://github.com/user/repo/releases/download/v1.0.1/myapp.AppImage"
    }
  }
}
```

Upload `latest.json` to GitHub Releases or a CDN.

## **6. Checking and Installing Updates in the App**  
### **JavaScript Implementation**  
`This part can be easily implemented using typescript or VUeJS(preferred), the choice will be made as per the then tech updates from tauri`
<br>
Prompt users for updates instead of automatically installing:

```js
import { check } from '@tauri-apps/plugin-updater';
import { relaunch } from '@tauri-apps/plugin-process';

async function checkForUpdates() {
    try {
        const update = await check();
        if (update) {
            const userChoice = confirm(
                `A new update (${update.version}) is available.\n\nWould you like to update now?`
            );

            if (userChoice) {
                await update.downloadAndInstall();
                alert("Update installed! Restarting the app...");
                await relaunch();
            } else {
                localStorage.setItem("postponeUpdate", "true"); //This is not gonna work for Tauri here, we can either declare a new variable in some store and store the information there, or we should use the Tauri-storage plugin (PREFERRED)
            }
        }
    } catch (error) {
        console.error("Update check failed:", error);
    }
}
```

### **7. Postpone Update Request Until Next Session**  

Check if the user postponed the update and re-prompt on the next session start:

```js
async function handlePostponedUpdate() {
    if (localStorage.getItem("postponeUpdate") === "true") {        // Storage plugin usage again
        const userChoice = confirm("You postponed the last update. Would you like to update now?");
        if (userChoice) {
            await checkForUpdates();
            localStorage.removeItem("postponeUpdate");    // Storage plugin usage again
        }
    }
}

// Call this on app startup
handlePostponedUpdate();
```

## **8. Expected Outcome**  
- Users **receive an update prompt** when a new release is available.
- If they **accept**, the update downloads and installs immediately.
- If they **decline**, they will be asked again in the **next session**.

---
