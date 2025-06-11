# Advanced Type Inference

Perfect! Now let’s go deep into **Advanced Type Inference**, including:

* `infer` keyword
* Template Literal Types
* Conditional Types
* Real-world examples using Node.js + Express + MongoDB

---

## ✅ 1. `infer` — Extract Types from Other Types

### 🧠 Think of it as:

> "Try to infer this inner type from a given structure"

### Example 1: Extract the type inside a `Promise<T>`

```ts
type UnwrapPromise<T> = T extends Promise<infer U> ? U : T;

type A = UnwrapPromise<Promise<string>>; // string
type B = UnwrapPromise<number>;          // number
```

---

### Example 2: Extract return type from a function

```ts
type GetReturnType<T> = T extends (...args: any[]) => infer R ? R : never;

function fetchData() {
  return { users: [] };
}

type Fetched = GetReturnType<typeof fetchData>; // { users: [] }
```

✅ `infer` works **only inside conditional types**, and it's super helpful when writing utility types.

---

## ✅ 2. Template Literal Types

Introduced in TS 4.1+, lets you **build new string literal types** dynamically.

```ts
type EventName = "click" | "hover" | "focus";
type HandlerName = `on${Capitalize<EventName>}`;

// "onClick" | "onHover" | "onFocus"
```

🔸 `Capitalize<T>`: Uppercases first letter
🔸 `Lowercase<T>`: Lowercases
🔸 `Uncapitalize<T>`: Makes first letter lowercase
🔸 `Uppercase<T>`: Full uppercase

### Real-world:

```ts
type Method = "get" | "post";
type RouteHandlerName = `${Uppercase<Method>}_Handler`;

// "GET_Handler" | "POST_Handler"
```

---

## ✅ 3. Conditional Types

Already seen with `infer`, but here’s the general syntax:

```ts
type IsString<T> = T extends string ? "yes" : "no";

type A = IsString<string>; // "yes"
type B = IsString<number>; // "no"
```

Now combine with `keyof`, `infer`, and more:

```ts
type ElementType<T> = T extends (infer U)[] ? U : T;

type A = ElementType<number[]>;   // number
type B = ElementType<string>;     // string
```

---

## ✅ 4. Real-World Example: MongoDB Model Field Types

```ts
type User = {
  id: string;
  email: string;
  isActive: boolean;
};

type OnlyBooleans<T> = {
  [K in keyof T as T[K] extends boolean ? K : never]: T[K]
};

type BooleanFields = OnlyBooleans<User>;
// { isActive: boolean }
```

🧠 This lets you dynamically build object types that include only certain field types.

---

## ✅ 5. Real-World Example: Auto-generate Express Route Types

```ts
type Route = "/users" | "/posts";

type GetRouteHandlerName<T extends string> = `get${Capitalize<T extends `/${infer R}` ? R : never>}`;

type Handler = GetRouteHandlerName<"/users">;  // "getUsers"
```

✅ With `infer` and Template Literal Types, you can build **framework-like logic**.

---

## ✅ 6. Combining Everything

Here’s a dynamic DTO generator:

```ts
type StripReadonly<T> = {
  -readonly [K in keyof T]: T[K];
};

type CreateDto<T> = {
  [K in keyof T as `create_${K & string}`]: T[K];
};

type User = {
  id: string;
  email: string;
};

type Dto = CreateDto<User>;
// {
//   create_id: string;
//   create_email: string;
// }
```

---

## ✅ Summary Table

| Feature                | Example                                  | Use Case                             |
| ---------------------- | ---------------------------------------- | ------------------------------------ |
| `infer`                | `T extends Promise<infer U> ? U : never` | Extract types                        |
| Template Literal Types | \`get\${Capitalize<string>}\`            | Dynamic strings for handlers, routes |
| Conditional Types      | `T extends U ? A : B`                    | Type branching                       |
| Indexed with as        | `[K in keyof T as ...]: T[K]`            | Rename or filter object properties   |

---

## 🚀 Final Tip

These advanced types allow you to:

* Build dynamic APIs
* Auto-generate type-safe interfaces
* Extract & filter MongoDB schema types
* Create custom validators, serializers, etc.
