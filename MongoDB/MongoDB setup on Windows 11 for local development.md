let’s start with **MongoDB setup on Windows 11 for local development**.

---

## 🖥 Install MongoDB on Windows 11

### 1. Download MongoDB Community Edition

* Go to: [MongoDB Download Center](https://www.mongodb.com/try/download/community)
* Choose:

  * Version: **Current (e.g., 8.0.x)**
  * Platform: **Windows x64**
  * Package: **MSI**
* Download & run the installer.

---

### 2. Run the Installer

* **Setup type**: choose **Complete**.
* Tick **Install MongoDB as a Service** (so it auto-starts).

  * Set it to run as **Network Service User**.
* Tick **Install MongoDB Compass** (GUI tool, optional but useful).
* Finish installation.

By default, MongoDB will:

* Install to: `C:\Program Files\MongoDB\Server\<version>\`
* Store data in: `C:\Program Files\MongoDB\Server\<version>\data`
* Run service: `MongoDB Server`
* FYI, do ENVIRONMENT VARIABLE registry.

---

### 3. Verify Installation

Open **Command Prompt / PowerShell** and run:

```powershell
mongosh
```

If it opens the Mongo shell, you’re good 👍

---

## 🔐 Enable Authentication (Optional for Dev, but Recommended)

By default, MongoDB on Windows runs **without authentication** (good for quick dev, bad for security).
Let’s enable username/password:

### 1. Create Admin User

Run:

```powershell
mongosh
```

Then in Mongo shell:

```javascript
use admin
db.createUser({
  user: "devAdmin",
  pwd: "StrongPass123!",
  roles: [ { role: "root", db: "admin" } ]
})
```

### 2. Enable Auth in Config

* Locate config: `C:\Program Files\MongoDB\Server\<version>\bin\mongod.cfg`
* Add:

```yaml
security:
  authorization: enabled
```

### 3. Restart MongoDB Service

```powershell
net stop MongoDB
net start MongoDB
```

### 4. Connect with User

```powershell
mongosh -u devAdmin -p StrongPass123! --authenticationDatabase admin
```

---

## 🎯 You’re Ready

* For **local dev only** → you can skip authentication if you want convenience.
* For **simulating production** → enable authentication to match server setup.

---
