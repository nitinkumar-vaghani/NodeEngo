You can list all MongoDB users (with their roles) directly from **mongosh**.

---

## 📋 1. Show users in the current database

If you are inside a specific DB (say `credit_control`):

```javascript
use credit_control
show users
```

➡️ This will print all users created **inside `credit_control`**, along with their roles.

---

## 📋 2. Show users with details (query form)

```javascript
use credit_control
db.getUsers()
```

Example output:

```json
[
  {
    "user" : "ccDevAdm",
    "roles" : [
      { "role" : "dbOwner", "db" : "credit_control" }
    ]
  }
]
```

---

## 📋 3. Show all users across all databases

Switch to the **`admin`** DB (requires a user with `userAdminAnyDatabase` or `root` role):

```javascript
use admin
db.getUsers({ showCredentials: false, showPrivileges: true })
```

➡️ This lists every user in the cluster, with their roles and which DB they belong to.

---

## ✅ Quick Summary

* `show users` → users of the current DB.
* `db.getUsers()` → users of the current DB (structured).
* `use admin; db.getUsers()` → all users across the cluster.

---
