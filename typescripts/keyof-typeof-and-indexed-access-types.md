# Keyof, Typeof and Indexed Access Types 
> — the backbone of how mapped types work

Let's learn **`keyof`**, **`typeof`**, and **indexed access types** — the **core building blocks** behind many powerful TypeScript features, especially when paired with generics and mapped types.

Let’s break each down **step-by-step**, with **practical examples** you’ll likely use in a Node.js + Express + MongoDB backend.

---

## ✅ 1. `keyof` – Get All Keys of a Type

### 🧠 Think of it as:

> "Give me a union of the keys of this type."

```ts
type User = {
  id: string;
  name: string;
  age: number;
};

type UserKeys = keyof User;
// "id" | "name" | "age"
```

You can now **constrain** values to just those keys:

```ts
function getValue(obj: User, key: keyof User) {
  return obj[key];
}

const u: User = { id: "1", name: "John", age: 30 };
getValue(u, "name"); // ✅ OK
getValue(u, "email"); // ❌ Error: "email" is not in keyof User
```

---

## ✅ 2. `typeof` – Get Type from a Variable or Value

### 🧠 Think of it as:

> "Use the type of this actual value."

```ts
const settings = {
  darkMode: true,
  language: "en",
};

type Settings = typeof settings;
// {
//   darkMode: boolean;
//   language: string;
// }
```

### 🔥 Real Use Case

You can reuse a config object’s structure without retyping:

```ts
const statusMap = {
  success: 200,
  notFound: 404,
  serverError: 500,
};

type StatusMap = typeof statusMap;
// { success: number; notFound: number; serverError: number }
```

---

## ✅ 3. Indexed Access Types – Extract the Type of a Key

### 🧠 Think of it as:

> "Give me the type of this property from this type."

```ts
type User = {
  id: string;
  name: string;
  age: number;
};

type NameType = User["name"];  // string
type IdOrAge = User["id" | "age"]; // string | number
```

This is **very powerful** when combined with generics and `keyof`:

```ts
function getProp<T, K extends keyof T>(obj: T, key: K): T[K] {
  return obj[key];
}

const user = { id: "123", name: "Alice", age: 25 };

const age = getProp(user, "age");   // inferred as number
const name = getProp(user, "name"); // inferred as string
```

---

## 💡 Combine All Three in Real Use

```ts
const permissions = {
  read: true,
  write: false,
  delete: false,
};

type Permissions = typeof permissions;
type PermissionKey = keyof Permissions;
type PermissionValue = Permissions[PermissionKey]; // boolean

function setPermission(key: PermissionKey, value: PermissionValue) {
  console.log(`Setting ${key} to ${value}`);
}

setPermission("read", true);     // ✅ OK
setPermission("execute", false); // ❌ Error
```

---

## 🔁 Dynamic Object Access

```ts
function pluck<T, K extends keyof T>(items: T[], key: K): T[K][] {
  return items.map(item => item[key]);
}

const users = [
  { id: 1, name: "A" },
  { id: 2, name: "B" },
];

const names = pluck(users, "name"); // string[]
const ids = pluck(users, "id");     // number[]
```

---

## ✅ Summary Table

| Feature                 | Purpose                       | Example                              |        |         |
| ----------------------- | ----------------------------- | ------------------------------------ | ------ | ------- |
| `keyof T`               | Get union of keys from a type | \`'id'                               | 'name' | 'age'\` |
| `typeof value`          | Get type from a value         | `typeof userConfig`                  |        |         |
| `T[K]`                  | Get type of a property/key    | `User["name"]` → `string`            |        |         |
| Generic + keyof + T\[K] | Safely access property types  | `getProp<T, K extends keyof T>(...)` |        |         |

---

## 🧪 Used Often In:

* 🧱 Type-safe MongoDB queries (e.g. `User[key]`)
* 🛡️ Generic validation/middleware
* 🔄 Dynamic mapping of keys
* ⚙️ Building utility functions like `pluck`, `get`, `set`
