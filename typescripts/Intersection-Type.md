# Intersection Type

Let's now dive into **Intersection Types** in `TypeScript` — a powerful concept that allows you to combine multiple types into one.

## ✅ Step 1: What is an Intersection Type?

An **Intersection Type** combines **two or more types** into a **single type** that has all the properties of the combined types.

> In TypeScript, it’s written using the `&` (ampersand) symbol.
```ts
type A = { name: string };
type B = { age: number };

type Person = A & B;
```
Now, `Person` must have **both** `name` and `age`.
## ✅ Step 2: Basic Example
```ts
type HasName = { name: string };
type HasAge = { age: number };

type User = HasName & HasAge;

const user: User = {
  name: "Nitin",
  age: 25
};
```
✅ The object must satisfy **all** properties from both types.
## ✅ Step 3: Combining Primitives (Not Very Useful)

Intersection with primitives behaves differently and is rarely useful:
```ts
type A = string;
type B = number;
type AB = A & B; // ❌ Not possible: no value can be both string & number
```
So, **intersection is mostly useful with object types.**
## ✅ Step 4: Functions with Intersection Types

You can define function parameters that **require multiple traits:**
```ts
type Admin = { admin: boolean };
type Employee = { employeeId: string };

function printAccess(user: Admin & Employee) {
  console.log("Admin:", user.admin);
  console.log("ID:", user.employeeId);
}

const u = { admin: true, employeeId: "EMP123" };
printAccess(u); // ✅ OK
```
## ✅ Step 5: Intersection of Interfaces

You can also intersect interfaces:
```ts
interface HasEmail {
  email: string;
}
interface HasUsername {
  username: string;
}

type Account = HasEmail & HasUsername;

const acc: Account = {
  email: "nitinvaghani34@gmail.com",
  username: "nitinkumar"
};
```
## ✅ Step 6: Use Case — Combining Types in Middleware

In Express (Node.js backend), you often enhance `req` objects with extra data:
```ts
import { Request } from "express";

type AuthData = { userId: string };
type ExtendedRequest = Request & AuthData;

function getProfile(req: ExtendedRequest) {
  console.log(req.userId); // ✅ works
}
```
This is a real-world use of intersections to **extend types safely.**
## ✅ Step 7: Caution with Overlapping Properties

If two types have the **same property name** but different types, it causes a conflict:
```ts
type A = { id: string };
type B = { id: number };

type AB = A & B; // ❌ Error: 'id' can't be both string & number
```
You should avoid this or make sure the types are compatible.
# ✅ Summary
| Feature               | Example                        | Purpose                              |
| --------------------- | ------------------------------ | ------------------------------------ |
| Basic intersection    | `type A = X & Y`               | Combine two types                    |
| For object types      | `{ name } & { age }`           | Merges both properties               |
| For extending request | `Request & { userId: string }` | Enhance existing type (e.g. Express) |
| Conflicts warning     | Same property, different types | Causes error                         |


# 🧠 Union vs Intersection: Quick Comparison
```md
| Feature          | Union (|)                 | Intersection (&)                     |
| -----------------| --------------------------| ------------------------------------ |
| Meaning          | One or the other          | Both at the same time                |
| Use case         | Flexible inputs           | Combine features                     |
| Example          | `string | number`         | `{ name } & { age }`                 |
| Real-world       | Input values, UI states   | Middleware, models, data enrichment  |
```
