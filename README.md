<p align="center">
    <img src="https://github.com/nitinkumar-vaghani/NodeEngo/blob/main/images/NodeEngo.png" width="300px">
</p>

<p align="center">
<h1 align="center">Welcome to NodeEngo!</h1>
</p>

Learn Node.JS as beginner and can become expert

## ✅ Tech Stack Overview
### 1. Node.js
* JavaScript runtime to run backend code.
* Handles asynchronous I/O (ideal for APIs and microservices).

### 2. Express.js
* Minimalist web framework built on Node.js.
* Simplifies route handling, middleware, and server setup.

### 3. MongoDB
* NoSQL database (document-based).
* Works great with JSON data, scalable, flexible schemas.
### 4. TypeScript
* Superset of JavaScript with types.
* Helps with safer, more maintainable code.
### 5. ES Modules (ESM)
* Native module system using import/export.
* Cleaner and modern than CommonJS (require).


🧱 Step-by-Step Setup Guide

### ✅ Step 1: Initialize Project

`mkdir my-ts-api && cd my-ts-api`

`npm init -y`

### ✅ Step 2: Enable ES Modules + TypeScript

Edit package.json:

```js
"type": "module",
"scripts": {
  "dev": "nodemon src/index.ts",
  "build": "tsc",
  "start": "node dist/index.js"
}
```

### ✅ Step 3: Install Dependencies

Runtime dependencies:

`npm install express mongoose dotenv`

Dev dependencies:

`npm install -D typescript ts-node nodemon @types/node @types/express`

### ✅ Step 4: Setup TypeScript

`npx tsc --init`

Edit tsconfig.json:

```js
{
  "compilerOptions": {
    "target": "ES2020",
    "module": "ES2022",
    "moduleResolution": "node",
    "outDir": "dist",
    "rootDir": "src",
    "esModuleInterop": true,
    "strict": true,
    "skipLibCheck": true
  },
  "include": ["src"]
}
```

### ✅ Step 5: Setup Folder Structure

```md
my-ts-api/
│
├── src/
│   ├── index.ts           # Entry point
│   ├── config/            # DB config & env
│   ├── routes/            # Express routers
│   ├── controllers/       # Request logic
│   ├── models/            # Mongoose schemas
│   └── utils/             # Helpers
├── .env                   # Secrets
├── tsconfig.json
└── package.json
```

### ✅ Step 6: Connect MongoDB

Install MongoDB locally or use MongoDB Atlas (cloud DB).

Create .env:

```md
PORT=5000
MONGO_URI=mongodb://localhost:27017/mydb
```

src/config/db.ts

```js
import mongoose from "mongoose";
import dotenv from "dotenv";

dotenv.config();

export const connectDB = async () => {
  try {
    await mongoose.connect(process.env.MONGO_URI!);
    console.log("MongoDB connected");
  } catch (err) {
    console.error(err);
    process.exit(1);
  }
};
```

### ✅ Step 7: Create Entry File

src/index.ts

```js
import express from "express";
import dotenv from "dotenv";
import { connectDB } from "./config/db.js";

dotenv.config();

const app = express();
const PORT = process.env.PORT || 5000;

app.use(express.json());

// Sample route
app.get("/", (_, res) => res.send("API running"));

// DB connect and start server
connectDB().then(() => {
  app.listen(PORT, () => {
    console.log(`Server running on http://localhost:${PORT}`);
  });
});
```

### ✅ Step 8: Run the Project

`npm run dev`

Visit http://localhost:5000 — you should see API running.


#### 🔧 Bonus: Nodemon Setup

Create nodemon.json:

```js
{
  "watch": ["src"],
  "ext": "ts",
  "ignore": ["dist"],
  "exec": "ts-node src/index.ts"
}
```

### ✅ Summary

```md
# Tool	Purpose
## Node.js	Backend runtime
## Express.js	Server & routing
## MongoDB	Database (document-based)
## TypeScript	Type safety, better tooling
## ES Modules	Modern module system
## Mongoose	ODM (MongoDB schema + model logic)
## Nodemon	Auto-restart on code changes
## ts-node	Runs TS directly for dev
```
