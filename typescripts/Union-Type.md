# Union Type

Let’s dive into **Union Types** in `TypeScript` — a super useful feature that allows flexibility while maintaining strong typing.

## ✅ Step 1: What is a Union Type?

A **Union Type** allows a variable to hold **more than one type** of value.

 > In TypeScript, it’s written using the `|` (pipe) symbol.
```ts
let value: string | number;
```
This means `value` can be a **string** *or* a **number**.
## ✅ Step 2: Basic Example of Union Type
```ts
let id: number | string;

id = 101;       // ✅ Valid
id = "ABC101";  // ✅ Also valid
id = true;      // ❌ Error: not allowed
```
This is useful when your data can come in multiple formats, like from a user input or external API.
## ✅ Step 3: Union Types in Functions
```ts
function printId(id: number | string): void {
  console.log("ID:", id);
}

printId(123);       // ✅
printId("XYZ123");  // ✅
```
But you can’t directly use type-specific methods unless you narrow the type (see next step).
## ✅ Step 4: Type Narrowing (using `typeof`)

Before using a method that belongs to only one type, check the type:
```ts
function handleInput(input: string | number) {
  if (typeof input === "string") {
    console.log(input.toUpperCase()); // ✅ Safe
  } else {
    console.log(input.toFixed(2));    // ✅ Safe
  }
}
```
This is called type narrowing, and it’s required when working with union types.
## ✅ Step 5: Union Types in Arrays

You can allow an array to contain mixed types:
```ts
let items: (string | number)[] = [1, "two", 3, "four"];
```
> Note: Parentheses are required to indicate that each item can be either type.

## ✅ Step 6: Union Type with Literal Types

You can limit a variable to only a few specific values:
```ts
type Status = "pending" | "success" | "error";

let jobStatus: Status;

jobStatus = "pending"; // ✅
jobStatus = "done";    // ❌ Error
```
This is very useful for APIs or flags.
## ✅ Step 7: Union Type with Type Aliases

To keep code clean, use a type alias:
```ts
type Id = string | number;

function findById(id: Id): void {
  console.log("Finding ID:", id);
}
```
## ✅ Step 8: Unions with Interfaces or Objects

You can define unions between object shapes:
```ts
type Dog = { kind: "dog"; bark(): void };
type Cat = { kind: "cat"; meow(): void };

type Animal = Dog | Cat;

function makeSound(animal: Animal) {
  if (animal.kind === "dog") {
    animal.bark();
  } else {
    animal.meow();
  }
}
```
This approach allows modeling complex behaviors (like in real APIs or event systems).

# 🧠 Summary
```md
| Concept                 | Syntax / Example                 | Meaning                                  |                                |
| ----------------------- | -------------------------------- | ---------------------------------------- | ------------------------------ |
| Union of types          | \`string                         | number\`                                 | Variable can be either type    |
| Union in function param | \`function x(v: A                | B)\`                                     | Input can be A or B            |
| Type narrowing          | `typeof`, `in`, `switch`         | Used to safely access properties/methods |                                |
| Union of objects        | \`type A = X                     | Y\`                                      | Polymorphism using union types |
| Literal union           | `"small" \| "medium" \| "large"` | Restrict value to specific strings       |                                |

```
