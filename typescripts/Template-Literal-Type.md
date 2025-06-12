# Template Literal Type


**Template Literal Types** in `TypeScript` are powerful and fun — they let you **construct new string types by combining literal strings and other types**. Think of them like string templates, but for types.

Let’s break it down step-by-step 👇

---

## ✅ 1. What is a Template Literal Type?

A **Template Literal Type** lets you define a **string-based type dynamically**, using a template syntax like this:

```ts
type Greeting = `Hello, ${string}`;
```

This type matches any string starting with `"Hello, "` followed by any string.

---

## ✅ 2. Basic Example

```ts
type Role = "admin" | "user";
type AccessMessage = `Access granted to ${Role}`;

const msg: AccessMessage = "Access granted to admin"; // ✅
const msg2: AccessMessage = "Access granted to guest"; // ❌ Error
```

✅ Template literals are **type-safe and constrained** to known values.

---

## ✅ 3. Useful Built-in String Helpers

You can combine template literals with **string utility types**:

| Utility           | Description                  | Example                         |
| ----------------- | ---------------------------- | ------------------------------- |
| `Capitalize<T>`   | Makes first letter uppercase | `Capitalize<"hello">` → "Hello" |
| `Uncapitalize<T>` | First letter lowercase       | `"Hello"` → `"hello"`           |
| `Uppercase<T>`    | All caps                     | `"api"` → `"API"`               |
| `Lowercase<T>`    | All lowercase                | `"API"` → `"api"`               |

```ts
type EventName = "click" | "focus";
type HandlerName = `on${Capitalize<EventName>}`;
// "onClick" | "onFocus"
```

---

## ✅ 4. Real-World Example – Dynamic Express Route Handler Names

```ts
type HttpMethod = "get" | "post" | "put" | "delete";
type RouteName = "user" | "post";

type HandlerName = `${HttpMethod}_${RouteName}`;
// "get_user" | "post_user" | "put_post" | "delete_post"
```

This is perfect for naming routes like: `get_user`, `post_post`, etc.

---

## ✅ 5. With `keyof` and Mapped Types

```ts
type User = {
  id: string;
  email: string;
  isAdmin: boolean;
};

type GetterNames<T> = {
  [K in keyof T as `get${Capitalize<string & K>}`]: () => T[K]
};

type UserGetters = GetterNames<User>;
/*
{
  getId: () => string;
  getEmail: () => string;
  getIsAdmin: () => boolean;
}
*/
```

💡 Used when creating auto-generated service classes or DTO mappers.

---

## ✅ 6. Narrowing String Patterns

```ts
type CssUnit = `${number}px` | `${number}em`;

const size1: CssUnit = "16px"; // ✅
const size2: CssUnit = "2em";  // ✅
const size3: CssUnit = "50%";  // ❌ Error
```

This is especially useful when building custom validators or configuration schemas.

---

## ✅ 7. Nested Templates

You can combine them as deeply as you need:

```ts
type Lang = "en" | "fr";
type Page = "home" | "about";

type LocalizedPath = `/${Lang}/${Page}`;
// "/en/home" | "/en/about" | "/fr/home" | "/fr/about"
```

---

## ✅ 8. Template Literals with `infer` (Advanced)

```ts
type ExtractLang<T> = T extends `/${infer L}/${string}` ? L : never;

type Lang1 = ExtractLang<"/en/home">; // "en"
type Lang2 = ExtractLang<"/fr/about">; // "fr"
```

✅ Extract part of a string pattern from a literal.

---

## ✅ 9. Combine with Union Filtering (`as`)

```ts
type Options = {
  darkMode: boolean;
  fontSize: number;
  theme: string;
};

type SetterNames = {
  [K in keyof Options as `set${Capitalize<K & string>}`]: (val: Options[K]) => void;
};

/*
{
  setDarkMode: (val: boolean) => void;
  setFontSize: (val: number) => void;
  setTheme: (val: string) => void;
}
*/
```

This lets you build **type-safe API client functions**, **state setters**, or **React-like hooks**.

---

## ✅ Summary

| Concept                   | Description                 | Example                            |                 |
| ------------------------- | --------------------------- | ---------------------------------- | --------------- |
| Basic template literal    | Type-safe string generation | \`Hello, \${string}\`              |                 |
| With unions               | Combine multiple values     | \`\${"get"                         | "post"}\_user\` |
| With `Capitalize`, etc.   | Format dynamically          | \`on\${Capitalize<Event>}\`        |                 |
| Mapped with `keyof`       | Auto-generate keys          | `getId`, `getEmail`, etc.          |                 |
| Use `infer` with patterns | Extract string segments     | `ExtractLang<"/en/home">` → `"en"` |                 |

---

## 🔥 Real Use Cases

* Dynamic route handling (REST APIs)
* Event naming
* MongoDB field access patterns (`user.name`, `user["email"]`)
* Auto-generating service classes
* Validation schemas
* Type-safe CSS or URL values
