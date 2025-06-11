# Utility Types

Let's now explore **Utility Types** in `TypeScript` — built-in helpers that let you **transform or simplify types** without rewriting everything manually.
## ✅ Step 1: What Are Utility Types?

**Utility types** are built into `TypeScript` and let you create new types by **modifying existing ones.** They help you:

  * Avoid repetition
  * Create cleaner types
  * Write safer, more expressive code

## ✅ Step 2: Commonly Used Utility Types (with Examples)

Let’s go one-by-one through the most used ones:
### 🔹 `Partial<Type>`

Makes **all properties optional.**
```ts
type User = {
  id: number;
  name: string;
  email: string;
};

type UpdateUser = Partial<User>;

const updateData: UpdateUser = {
  email: "nitinvaghani34@gmail.com" // ✅ only email, rest are optional
};
```
📌 Useful for **patch/update** APIs.
### 🔹 `Required<Type>`

Makes **all properties required** (even if optional originally).
```ts
type User = {
  id: number;
  name?: string;
};

type StrictUser = Required<User>;

const user: StrictUser = {
  id: 1,
  name: "Nitin" // ✅ must include name now
};
```
📌 Useful when you want to enforce completeness of data.
### 🔹 `Readonly<Type>`

Makes **all properties read-only** (cannot be reassigned).
```ts
type Config = {
  host: string;
  port: number;
};

const config: Readonly<Config> = {
  host: "localhost",
  port: 3000
};

config.port = 4000; // ❌ Error: Cannot assign to 'port'
```
📌 Useful for configs/constants.
### 🔹 `Pick<Type, Keys>`

Creates a type by **picking specific properties.**
```ts
type User = {
  id: number;
  name: string;
  email: string;
};

type UserSummary = Pick<User, "id" | "name">;

const summary: UserSummary = {
  id: 1,
  name: "Nitin"
};
```
📌 Useful when exposing only a **subset of fields** (e.g., in APIs).
### 🔹 `Omit<Type, Keys>`

Creates a type by **removing specific properties.**
```ts
type User = {
  id: number;
  name: string;
  password: string;
};

type PublicUser = Omit<User, "password">;

const user: PublicUser = {
  id: 1,
  name: "Nitin"
};
```
📌 Great for hiding sensitive or unwanted fields.
### 🔹 `Record<Keys, Type>`

Creates an object type with **specific keys and a shared type.**
```ts
type Role = "admin" | "editor" | "viewer";

type Permissions = Record<Role, boolean>;

const perms: Permissions = {
  admin: true,
  editor: false,
  viewer: false
};
```
📌 Powerful when you need to enforce a **mapping** of values.
### 🔹 `Exclude<UnionType, ExcludedMembers>`

Removes types from a union.
```ts
type Status = "draft" | "published" | "archived";

type ActiveStatus = Exclude<Status, "archived">;
// "draft" | "published"
```
### 🔹 `Extract<UnionType, ExtractedMembers>`

Keeps **only the matching types** from a union.
```ts
type Status = "draft" | "published" | "archived";

type Viewable = Extract<Status, "published" | "archived">;
// "published" | "archived"
```
### 🔹 `NonNullable<Type>`

Removes `null` and `undefined` from a type.
```ts
type Name = string | null | undefined;

type SafeName = NonNullable<Name>; // string only
```
# 🧠 Summary Table
| Utility Type     | Purpose                             |
| ---------------- | ----------------------------------- |
| `Partial<T>`     | Makes all props optional            |
| `Required<T>`    | Makes all props required            |
| `Readonly<T>`    | Prevents reassigning values         |
| `Pick<T, K>`     | Pick only selected keys             |
| `Omit<T, K>`     | Remove selected keys                |
| `Record<K, T>`   | Map fixed keys to same type         |
| `Exclude<T, U>`  | Remove types from a union           |
| `Extract<T, U>`  | Keep only specific types in a union |
| `NonNullable<T>` | Remove `null` and `undefined`       |

# 🧪 Real-World Use Example
```ts
// In API controller
type FullUser = {
  id: string;
  name: string;
  email: string;
  password: string;
};

type SafeUser = Omit<FullUser, "password">;

function sendUserToClient(user: FullUser): SafeUser {
  const { password, ...safe } = user;
  return safe;
}
```
