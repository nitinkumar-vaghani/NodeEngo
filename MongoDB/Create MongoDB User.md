You don’t want a **root user**, but instead a **dedicated user with full rights only on `cc_test` database**. That’s the correct way for application-level access.

Here’s how you can do it in **mongosh**:

---

## 1. Switch to Your New Database

```javascript
use cc_test
```

---

## 2. Create a User With Full Access on This DB Only

```javascript
db.createUser({
  user: "cc_test_user",
  pwd: "StrongPass#2025!",
  roles: [ { role: "dbOwner", db: "cc_test" } ]
})
```

* `dbOwner` → gives **all privileges on this single database** (create collections, insert, update, delete, indexes, etc.).
* The user **cannot access admin or other databases**.

---

## 3. Connect Using This User

From shell:

```powershell
mongosh -u cc_test_user -p StrongPass#2025! --authenticationDatabase cc_test
```

From your app connection string:

```
mongodb://cc_test_user:StrongPass#2025!@localhost:27017/cc_test
```

---

## 4. Verify Permissions

Inside `mongosh` (logged in as `cc_test_user`):

```javascript
use cc_test
db.test.insertOne({hello: "world"})   // ✅ should work

use admin
db.system.users.find()                // ❌ should fail (no access)
```

---
