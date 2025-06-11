# Discriminated Unions

**Discriminated Unions** are one of the most powerful features in TypeScript, especially for building **type-safe APIs**, **forms**, **Redux-like state machines**, and more.

---

## ✅ Step 1: What Are Discriminated Unions?

A **Discriminated Union** (also called a *Tagged Union*) is a **union of object types** that share a **common property** (the “discriminant”) which has **distinct literal values**.

This lets TypeScript **narrow the type automatically** when you check that property.

---

## ✅ Step 2: Basic Structure

```ts
type Square = { kind: "square"; size: number };
type Circle = { kind: "circle"; radius: number };
type Shape = Square | Circle;
```

* All types have a common `kind` property.
* The `kind` has a literal value: `"square"` or `"circle"`.

This is your **discriminant**.

---

## ✅ Step 3: Narrowing Using `switch`

```ts
function getArea(shape: Shape): number {
  switch (shape.kind) {
    case "square":
      return shape.size ** 2;
    case "circle":
      return Math.PI * shape.radius ** 2;
  }
}
```

✅ TypeScript infers the correct type inside each `case` block.

---

## ✅ Step 4: Adding More Variants

```ts
type Rectangle = { kind: "rectangle"; width: number; height: number };

type Shape = Square | Circle | Rectangle;

function getArea(shape: Shape): number {
  switch (shape.kind) {
    case "square":
      return shape.size ** 2;
    case "circle":
      return Math.PI * shape.radius ** 2;
    case "rectangle":
      return shape.width * shape.height;
  }
}
```

---

## ✅ Step 5: Exhaustiveness Checking (💡 Super Useful!)

You can ensure **all cases are handled** by adding a `never` fallback:

```ts
function getArea(shape: Shape): number {
  switch (shape.kind) {
    case "square": return shape.size ** 2;
    case "circle": return Math.PI * shape.radius ** 2;
    case "rectangle": return shape.width * shape.height;
    default:
      const _exhaustive: never = shape;
      throw new Error(`Unhandled shape: ${_exhaustive}`);
  }
}
```

❌ If a new shape is added later (like `triangle`), TypeScript will throw an error until it’s handled.

---

## ✅ Step 6: Real-World Example (API Input Handling)

```ts
type TextInput = {
  type: "text";
  placeholder: string;
};

type NumberInput = {
  type: "number";
  min: number;
  max: number;
};

type CheckboxInput = {
  type: "checkbox";
  label: string;
};

type FormElement = TextInput | NumberInput | CheckboxInput;

function renderInput(input: FormElement) {
  switch (input.type) {
    case "text":
      return `<input type="text" placeholder="${input.placeholder}">`;
    case "number":
      return `<input type="number" min="${input.min}" max="${input.max}">`;
    case "checkbox":
      return `<label><input type="checkbox">${input.label}</label>`;
  }
}
```

🧠 This is how form libraries and design systems use TypeScript to ensure complete safety!

---

## ✅ Step 7: Why Discriminated Unions Are Powerful

| Benefit                       | Why It Matters                                      |
| ----------------------------- | --------------------------------------------------- |
| 🧠 Automatic type narrowing   | Less type assertions, more safety                   |
| 💡 Exhaustive checks          | Catches bugs when adding new cases                  |
| 🔁 Reusable for modeling APIs | Input types, responses, actions, validation schemas |
| 📦 Works with switch & if     | Flexible control flow                               |

---

## ✅ Summary

Discriminated unions require:

* A **shared field** (like `kind`, `type`, `status`, etc.)
* That field must have **literal string (or number) values**
* TypeScript uses that to **narrow the type automatically**

