# Primitive Type

Let’s go step-by-step to understand what primitive types are in **TypeScript**, with clear explanations and examples.

## ✅ Step 1: What is a Primitive Type?

A **primitive type** is a **basic data type** that is **not an object** and is **immutable**. It represents a **single value** like a number, string, or boolean.

**Immutable:** You can't change a primitive value once it's created (e.g. strings cannot be modified directly).

These types are **built into** the language and form the foundation for handling data in `TypeScript` and `JavaScript`.

## ✅ Step 2: List of Primitive Types in TypeScript

TypeScript has the same primitive types as JavaScript, Here are the **7 primitive types**:

```md
| Type        | Description                  | Example                     |
| ----------- | ---------------------------- | --------------------------- |
| `string`    | Sequence of characters       | `"Hello"`                   |
| `number`    | Numeric values (int/float)   | `10`, `3.14`                |
| `boolean`   | Logical true/false           | `true`, `false`             |
| `null`      | Intentional absence of value | `null`                      |
| `undefined` | Uninitialized value          | `undefined`                 |
| `bigint`    | Very large integers          | `123n`, `9007199254740991n` |
| `symbol`    | Unique identifiers           | `Symbol("id")`              |
```

## ✅ Step 3: Examples of Each Primitive
🔹 1. `string`
```ts
let firstName: string = "Alice";
```
🔹 2. `number`
```ts
let age: number = 25;
let pi: number = 3.14;
```
🔹 3. `boolean`
```ts
let isLoggedIn: boolean = true;
```
🔹 4. `null`
```ts
let emptyValue: null = null;
```
🔹 5. `undefined`
```ts
let somethingUndefined: undefined = undefined;
```
🔹 6. `bigint` (used for large integers)
```ts
let bigNumber: bigint = 1234567890123456789012345678901234567890n;
```
🔹 7. `symbol` (for unique keys)
```ts
let uniqueId: symbol = Symbol("id");
```
## ✅ Step 4: Characteristics of Primitive Types

```md
| Feature         | Primitive Types |
| --------------- | --------------- |
| Immutable       | ✅ Yes           |
| Stored by value | ✅ Yes           |
| Lightweight     | ✅ Yes           |
| Methods?        | ❌ Not directly  |
```

Example:
```ts
let str = "hello";
str.toUpperCase(); // Returns "HELLO", but doesn't modify `str`
console.log(str);  // still "hello"
```
## ✅ Step 5: Checking Type with `typeof`

You can use `typeof` to check a primitive’s type:
```ts
console.log(typeof "hello");      // string
console.log(typeof 42);           // number
console.log(typeof true);         // boolean
console.log(typeof undefined);    // undefined
console.log(typeof null);         // object ← (weird JS quirk)
console.log(typeof Symbol("x"));  // symbol
console.log(typeof 123n);         // bigint
```
## ✅ Step 6: Primitive vs Object (Non-Primitive)

```md
| Feature   | Primitive (`string`, `number`) | Object (`{}`, `[]`) |
| --------- | ------------------------------ | ------------------- |
| Stored by | Value                          | Reference           |
| Mutable   | ❌ No                          | ✅ Yes             |
| Example   | `"hello"`                      | `{ name: "John" }`  |
```

## ✅ Step 7: Union of Primitives

You can combine primitive types:
```ts
let value: string | number;
value = "hello";
value = 42;
```
This is useful for flexible input handling.
🧠 Bonus Tip: Use Literal Types with Primitives
```ts
You can restrict values like this:

type YesOrNo = "yes" | "no";

let response: YesOrNo = "yes"; // ✅
response = "maybe"; // ❌ Error
```

# 🔚 Summary

  * Primitive types are the simplest data types.
  * Immutable, compared by value, and not objects.
  * Use them as the building blocks of more complex types.
  * TypeScript enhances them with type annotations and unions.
