In MongoDB, user accounts are tied to the database where they were created.
Since you created `ccDevAdm` in the **`credit_control`** database, you must manage it from that database.

---

## 🛠️ Option 1: Change the Password (recommended)

If you just want to update the password, no need to delete the user:

```javascript
use credit_control
db.updateUser("ccDevAdm", { pwd: "New$StrongPass#2025" })
```

---

## 🗑️ Option 2: Delete the User

If you want to drop the user and recreate it:

```javascript
use credit_control
db.dropUser("ccDevAdm")
```

Then create again:

```javascript
db.createUser({
  user: "ccDevAdm",
  pwd: "New$StrongPass#2025",
  roles: [ { role: "dbOwner", db: "credit_control" } ]
})
```

---

## ⚠️ Notes

* Make sure you are logged in as a user that has privileges to manage users in `credit_control` (e.g. a root user or a `dbOwner` of `credit_control`).
* If you’re connected as `ccDevAdm` itself, you **cannot drop yourself** while logged in — you need an admin/root user.

---
