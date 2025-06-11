# Mapped Types

Diving into **Mapped Types** will help you understand the foundation behind many of TypeScript’s powerful features like `Partial`, `Readonly`, `Record`, etc.

---

## ✅ Step 1: What are Mapped Types?

Mapped Types let you **transform an existing type** into a new one by **looping over its keys** using `keyof` and modifying their properties.

### 🧠 Think of it like:

> “Create a new type based on another type, changing each property in a certain way.”

---

## ✅ Step 2: Basic Mapped Type Syntax

```ts
type MappedType<T> = {
  [P in keyof T]: T[P];
};
```

* `T` is the input type.
* `P in keyof T` loops over each property key.
* `T[P]` is the type of that key.

### 🔍 Example:

```ts
type User = {
  name: string;
  age: number;
};

type Clone = {
  [K in keyof User]: User[K];
};

// Clone is exactly same as User
```

---

## ✅ Step 3: Build Your Own Utility: `Readonly`

```ts
type MyReadonly<T> = {
  readonly [K in keyof T]: T[K];
};

type Todo = {
  title: string;
  done: boolean;
};

type ReadonlyTodo = MyReadonly<Todo>;
```

```ts
const todo: ReadonlyTodo = {
  title: "Learn TS",
  done: false
};

todo.title = "New Title"; // ❌ Error: Cannot assign
```

---

## ✅ Step 4: `Partial<T>` as Mapped Type

```ts
type MyPartial<T> = {
  [K in keyof T]?: T[K];
};

type User = {
  id: string;
  name: string;
};

type PartialUser = MyPartial<User>;

// id and name are optional now
```

---

## ✅ Step 5: Use `as` to Rename Keys (Advanced)

You can even rename keys with `as` in mapped types.

```ts
type PrefixProps<T> = {
  [K in keyof T as `prefix_${string & K}`]: T[K];
};

type Options = {
  mode: string;
  retry: boolean;
};

type Prefixed = PrefixProps<Options>;
```

🔍 Result:

```ts
// type Prefixed = {
//   prefix_mode: string;
//   prefix_retry: boolean;
// }
```

---

## ✅ Step 6: Build Your Own Utility: `Record`

```ts
type MyRecord<K extends string | number | symbol, T> = {
  [P in K]: T;
};

type Roles = "admin" | "user";

type RoleFlags = MyRecord<Roles, boolean>;

// Equivalent to:
type RoleFlagsNative = {
  admin: boolean;
  user: boolean;
};
```

---

## ✅ Step 7: Conditional Mapped Types

You can make mapped types conditional per property:

```ts
type OptionalFlags<T> = {
  [K in keyof T]?: T[K] extends boolean ? never : T[K];
};

type Config = {
  debug: boolean;
  name: string;
};

type OptionalConfig = OptionalFlags<Config>;

// Result: { debug?: never; name?: string }
```

---

## ✅ Summary Table

| Mapped Type Feature          | Purpose                                 |
| ---------------------------- | --------------------------------------- |
| `[K in keyof T]: T[K]`       | Clone/Transform a type                  |
| `readonly [K in keyof T]`    | Add readonly modifier                   |
| `[K in keyof T]?`            | Make properties optional                |
| `[K in keyof T as NewK]`     | Rename keys during mapping              |
| `Record<K, T>` (custom)      | Create object with keys `K` of type `T` |
| Conditional logic inside map | Add dynamic control per property        |

---

## 🧪 Real-World Use: API Field Masking

```ts
type User = {
  id: number;
  name: string;
  password: string;
};

type HideFields<T> = {
  [K in keyof T as K extends "password" ? never : K]: T[K];
};

type SafeUser = HideFields<User>;

// SafeUser = { id: number; name: string }
```

---

## ✅ Why Mapped Types Matter

Mapped types give you:

* 🔁 Reusability
* 🔧 Dynamic behavior (adapt to data shape)
* 🧠 Foundation for built-in utilities
* 🚀 Custom utilities tailored to your codebase
