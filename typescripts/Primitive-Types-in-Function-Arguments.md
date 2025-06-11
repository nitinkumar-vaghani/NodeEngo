# Primitive Types in Function Arguments

Let's understand step-by-step and see how primitive types are used in function arguments, and then compare them with object types and interfaces.

## ✅ Step 1: Using Primitive Types in Function Arguments

In TypeScript, you can define types of function parameters using primitives like string, number, boolean, etc.

🔹 Example: Basic Usage

```ts
function greet(name: string): string {
  return `Hello, ${name}!`;
}

console.log(greet("Alice")); // Hello, Alice!
```

🔹 Another Example with Multiple Primitives

```ts
function calculateDiscount(price: number, isMember: boolean): number {
  if (isMember) {
    return price * 0.9; // 10% discount
  }
  return price;
}

console.log(calculateDiscount(100, true)); // 90
```

These examples are purely using primitive types for inputs and outputs.

## ✅ Step 2: Type Inference

TypeScript can often infer types, but explicit annotation improves clarity and prevents bugs:

```ts
// Implicit type (inferred)
const age = 30;

// Explicit type
const name: string = "John";
```
When using functions, explicit typing of arguments is always recommended.

## ✅ Step 3: Return Types as Primitives

You can also define the return type of a function using primitives:

```ts
function isAdult(age: number): boolean {
  return age >= 18;
}
```
## ✅ Step 4: Comparing with Object Types

Primitive values are simple and passed by value, whereas objects are more complex and passed by reference.

🔸 Primitive (passed by value)

```ts
function double(n: number) {
  n = n * 2;
  return n;
}

let value = 10;
double(value);
console.log(value); // 10 — original value remains unchanged
```
🔸 Object (passed by reference)
```ts
function updateUser(user: { name: string }) {
  user.name = "Updated";
}

const person = { name: "Original" };
updateUser(person);
console.log(person.name); // "Updated" — object was modified
```

## ✅ Step 5: Interfaces for Object Types

Use interface or type to define object shapes:
```ts
interface User {
  name: string;
  age: number;
}

function printUser(user: User): void {
  console.log(`${user.name} is ${user.age} years old.`);
}

printUser({ name: "Nitin", age: 25 });
```
## ✅ Step 6: Mixing Primitives and Objects

Many functions combine both:
```ts
interface Product {
  name: string;
  price: number;
}

function getDiscountedPrice(product: Product, discount: number): number {
  return product.price - discount;
}
```
## ✅ Step 7: Optional and Default Parameters

You can also make primitive parameters optional or give defaults:

```ts
function sayHello(name: string = "Guest"): string {
  return `Hello, ${name}`;
}

sayHello();         // Hello, Guest
sayHello("Nitin");  // Hello, Nitin
```

# 🧠 Summary

```md
| Feature              | Primitive Example  | Object Example      |
| -------------------- | ------------------ | ------------------- |
| Type                 | `string`, `number` | `interface`, `type` |
| Mutability           | Immutable          | Mutable             |
| Passed as            | Value              | Reference           |
| Used in functions as | Direct value       | Structured data     |
```
