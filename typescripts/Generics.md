# Generics

Let’s dive into **Generics** in TypeScript — one of the **most important tools** for writing reusable, scalable, and type-safe code in any real-world Node.js/Express/MongoDB app.

---

## ✅ Step 1: What Are Generics?

Generics allow you to write **functions**, **classes**, and **types** that **work with any data type**, while still preserving **strong typing**.

> Think of Generics as **type placeholders** — like function parameters, but for types.

---

## ✅ Step 2: Basic Generic Function

```ts
function identity<T>(value: T): T {
  return value;
}

const a = identity<string>("Hello");  // T is string
const b = identity<number>(42);       // T is number
```

TypeScript will **infer** the type if you don’t provide it explicitly:

```ts
const result = identity(true); // T is inferred as boolean
```

---

## ✅ Step 3: Generic Arrays

```ts
function firstItem<T>(arr: T[]): T {
  return arr[0];
}

const num = firstItem([10, 20, 30]);       // number
const str = firstItem(["a", "b", "c"]);    // string
```

✅ This works for **any array** type — and ensures type safety of what’s returned.

---

## ✅ Step 4: Generics with Multiple Types

```ts
function pair<A, B>(first: A, second: B): [A, B] {
  return [first, second];
}

const p1 = pair("x", 1);       // [string, number]
const p2 = pair(true, "yes");  // [boolean, string]
```

---

## ✅ Step 5: Generic Constraints (Limit the Accepted Types)

You can restrict `T` using `extends`.

```ts
function getLength<T extends { length: number }>(item: T): number {
  return item.length;
}

getLength("hello");           // ✅ string has length
getLength([1, 2, 3]);         // ✅ array has length
getLength(123);               // ❌ Error: number has no 'length'
```

---

## ✅ Step 6: Generic Interfaces

```ts
interface ApiResponse<T> {
  success: boolean;
  data: T;
}

const response1: ApiResponse<string> = {
  success: true,
  data: "All good"
};

const response2: ApiResponse<number[]> = {
  success: true,
  data: [1, 2, 3]
};
```

✅ This is super common in Express/MongoDB APIs where the response data type can vary.

---

## ✅ Step 7: Generic Classes

```ts
class Box<T> {
  content: T;

  constructor(value: T) {
    this.content = value;
  }

  get(): T {
    return this.content;
  }
}

const stringBox = new Box("hi");   // Box<string>
const numberBox = new Box(123);    // Box<number>
```

---

## ✅ Step 8: Default Generic Types

```ts
type Maybe<T = string> = T | null;

const a: Maybe = "hello";    // string | null
const b: Maybe<number> = 5;  // number | null
```

You can provide a **default type** in case it's not specified.

---

## ✅ Step 9: Real-World Example (MongoDB + Express)

```ts
interface PaginatedResponse<T> {
  results: T[];
  total: number;
  page: number;
  pageSize: number;
}

function sendPaginated<T>(data: T[]): PaginatedResponse<T> {
  return {
    results: data,
    total: data.length,
    page: 1,
    pageSize: data.length,
  };
}

sendPaginated<string>(["a", "b", "c"]); // returns PaginatedResponse<string>
```

---

## ✅ Summary

| Concept                    | Example                        |
| -------------------------- | ------------------------------ |
| Basic Generic              | `function<T>(arg: T): T`       |
| Inferred Type              | `identity(123)`                |
| Multiple Generics          | `<A, B>(a: A, b: B): [A, B]`   |
| Constraints with `extends` | `T extends { length: number }` |
| Generic Interface          | `ApiResponse<T>`               |
| Generic Class              | `class Box<T>`                 |
| Default Generic            | `Maybe<T = string>`            |

---

## 🔥 When to Use Generics?

* Writing **reusable logic** (e.g. utility functions, middleware, repositories)
* Building **custom Express middleware** that work across models
* Creating **MongoDB models** and service layers with flexible types
* Designing **API response structures**
