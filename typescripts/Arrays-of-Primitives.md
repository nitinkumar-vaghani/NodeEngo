# Arrays of Primitives

Let’s now dive into **Arrays of Primitives** in `TypeScript`. This is an essential topic when working with lists of values like names, IDs, scores, etc.

## ✅ Step 1: What is an Array of Primitives?

An **array of primitives** is simply a list (array) that contains **primitive values**, such as:
  * `string[]` → list of strings
  * `number[]` → list of numbers
  * `boolean[]` → list of booleans
    
## ✅ Step 2: Declaring Arrays of Primitives

You can declare them in two ways:

🔹 Using square brackets (`Type[]`)
```ts
let names: string[] = ["Alice", "Bob", "Charlie"];
let ages: number[] = [25, 30, 35];
let flags: boolean[] = [true, false, true];
```
🔹 Using `Array<Type>` (generic syntax)
```ts
let names: Array<string> = ["Alice", "Bob"];
let ages: Array<number> = [10, 20, 30];
```
Both styles are valid — Type[] is more common.

## ✅ Step 3: Common Operations
🔸 Looping through arrays
```ts
const names: string[] = ["Nitin", "Vin", "Devil"];

for (const name of names) {
  console.log("Hi", name);
}
```
🔸 Adding/removing items
```ts
let scores: number[] = [80, 90];
scores.push(100); // [80, 90, 100]
scores.pop();     // [80, 90]
```
🔸 Using array methods
```ts
let marks: number[] = [10, 20, 30];
let doubled = marks.map(mark => mark * 2); // [20, 40, 60]
```
## ✅ Step 4: Readonly Arrays

If you don’t want the array to be modified:

```ts
const colors: readonly string[] = ["red", "green", "blue"];
// colors.push("yellow"); ❌ Error: Cannot push to a readonly array
```
Or with generic:
```ts
const ids: ReadonlyArray<number> = [1, 2, 3];
```
## ✅ Step 5: Array of Optional Primitives

You can allow `undefined` or `null` values in the array:

```ts
let values: (number | undefined)[] = [1, undefined, 3];
let names: (string | null)[] = ["A", null, "C"];
```
This is useful for situations where data might be incomplete.

## ✅ Step 6: Multidimensional Arrays

You can have arrays of arrays too:

```ts
let matrix: number[][] = [
  [1, 2],
  [3, 4],
  [5, 6]
];
```

Each element is itself an array of numbers.

## ✅ Step 7: Type Inference with Arrays

If you don’t explicitly type it, TypeScript will infer from values:

```ts
const tags = ["node", "express"]; // inferred as string[]
```
But always prefer typing it for clarity and future safety:
```ts
const tags: string[] = ["node", "express"];
```
## ✅ Step 8: Function Parameters with Array of Primitives

You can pass arrays into functions like this:
```ts
function getTotal(prices: number[]): number {
  return prices.reduce((sum, p) => sum + p, 0);
}

console.log(getTotal([10, 20, 30])); // 60
```

# ✅ Summary
```md
| Feature          | Example                      | Description                         |                      |
| ---------------- | ---------------------------- | ----------------------------------- | -------------------- |
| Basic array      | `let nums: number[] = [1,2]` | Array of numbers                    |                      |
| Generic style    | `Array<string>`              | Alternate array syntax              |                      |
| Readonly array   | `readonly string[]`          | Prevent modifications               |                      |
| Optional entries | \`(number                    | undefined)\[]\`                     | Allow missing values |
| Multidimensional | `string[][]`                 | Matrix or nested arrays             |                      |
| In function      | `function fn(x: number[])`   | Accept array of primitives as param |                      |
```
