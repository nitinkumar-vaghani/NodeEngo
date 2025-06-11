# Type Guards
Let’s explore **Type Guards** — one of the most powerful features in `TypeScript` to work safely with **union types** and complex type structures.
## ✅ Step 1: What Are Type Guards?

A **Type Guard** is a way to **narrow down a variable’s type** within a condition. It tells TypeScript:

> “Inside this block, treat this variable as this specific type.”

Type guards are essential when using **union types**, where a variable can be more than one type.
## ✅ Step 2: Example Without Type Guard (Error ❌)
```ts
function formatId(id: string | number) {
  return id.toUpperCase(); // ❌ Error: Property 'toUpperCase' does not exist on type 'string | number'
}
```
TypeScript doesn't know if `id` is a `string` or `number`, so it throws an error.
## ✅ Step 3: Using `typeof` Type Guard

🔹 **`typeof` works with: `string`, `number`, `boolean`, `symbol`, `undefined`, `bigint`, `function`**
```ts
function formatId(id: string | number) {
  if (typeof id === "string") {
    return id.toUpperCase(); // ✅ Safe
  } else {
    return id.toFixed(2);    // ✅ Safe
  }
}
```
Now, TypeScript knows what type `id` is in each block.
## ✅ Step 4: Using `in` Operator Type Guard (for Objects)
🔹 `in` checks if a **property exists** in the object.
```ts
type Dog = { bark: () => void };
type Cat = { meow: () => void };

function speak(animal: Dog | Cat) {
  if ("bark" in animal) {
    animal.bark(); // ✅ It's a Dog
  } else {
    animal.meow(); // ✅ It's a Cat
  }
}
```
## ✅ Step 5: Using `instanceof` Type Guard
🔹 `instanceof` **works for class-based types.**
```ts
class Car {
  drive() { console.log("Driving"); }
}
class Boat {
  sail() { console.log("Sailing"); }
}

function move(vehicle: Car | Boat) {
  if (vehicle instanceof Car) {
    vehicle.drive();
  } else {
    vehicle.sail();
  }
}
```
## ✅ Step 6: Custom Type Guards (with `is` keyword)

You can define your own type guard functions:
```ts
type Admin = { role: "admin"; adminLevel: number };
type User = { role: "user"; access: string };

function isAdmin(account: Admin | User): account is Admin {
  return account.role === "admin";
}

function accessPanel(account: Admin | User) {
  if (isAdmin(account)) {
    console.log("Admin level:", account.adminLevel);
  } else {
    console.log("User access:", account.access);
  }
}
```
🔥 **Why it's useful:**

  * Reusable
  * Readable
  * Works with complex conditions

## ✅ Step 7: Discriminated Unions (Special Case of Type Guard)

If your union types have a **common** `kind` property, TypeScript can narrow them **automatically:**
```ts
type Square = { kind: "square"; size: number };
type Circle = { kind: "circle"; radius: number };
type Shape = Square | Circle;

function area(shape: Shape): number {
  switch (shape.kind) {
    case "square": return shape.size ** 2;
    case "circle": return Math.PI * shape.radius ** 2;
  }
}
```
This is a clean and scalable pattern for working with unions.
# 🧠 Summary
| Type Guard Method      | Usage Example                  | Works With              |
| ---------------------- | ------------------------------ | ----------------------- |
| `typeof`               | `typeof x === "string"`        | primitives              |
| `in` operator          | `"bark" in animal`             | objects with properties |
| `instanceof`           | `x instanceof Car`             | classes / constructors  |
| custom function (`is`) | `isAdmin(user): user is Admin` | complex objects         |
| discriminated union    | `shape.kind === "circle"`      | tagged object unions    |

# ✅ Real-Life Example (Express Middleware)
```ts
type AuthedRequest = Request & { userId?: string };

function isAuthed(req: Request): req is AuthedRequest {
  return "userId" in req;
}

function getDashboard(req: Request) {
  if (isAuthed(req)) {
    console.log("User ID:", req.userId);
  } else {
    console.log("Not authenticated");
  }
}
```
