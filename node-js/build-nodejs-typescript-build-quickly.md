Great—here’s a clean, production-ready way to run **one codebase / one build** with **environment-specific config** for *local, staging, production*.

I’ll give you everything you need:

1. `.env` layout
2. Type-safe env loader (`src/config/env.mts`) using **dotenv-flow + zod**
3. `tsconfig` for strict ESM builds
4. `package.json` scripts for dev/build/run per environment (no code duplication)
5. Notes for PM2/systemd (optional)

---

# 1) Environment files

Create these at the project root:

```
.env                      # shared defaults (lowest priority)
.env.local                # local overrides
.env.staging              # staging overrides
.env.production           # production overrides
.env.example              # template for teammates/CI
```

Example contents:

**.env**

```
APP_NAME=node-engo
APP_PORT=8089
APP_ENV=local
CORS_ALLOWED_ORIGINS=http://localhost:5173
REDIS_URL=redis://127.0.0.1:6379
DEFAULT_MONGO_URI=mongodb://127.0.0.1:27017/node_engo_master
JWT_SECRET=super-secret
UPLOADS_PUBLIC_BASE=http://localhost:8089/uploads
```

**.env.local**

```
APP_ENV=local
CORS_ALLOWED_ORIGINS=http://localhost:5173
DEFAULT_MONGO_URI=mongodb://127.0.0.1:27017/node_engo_master
```

**.env.staging**

```
APP_ENV=staging
CORS_ALLOWED_ORIGINS=https://staging.your-ui.com
DEFAULT_MONGO_URI=mongodb://10.0.0.10:27017/node_engo_master
UPLOADS_PUBLIC_BASE=https://staging.api.your-domain.com/uploads
```

**.env.production**

```
APP_ENV=production
CORS_ALLOWED_ORIGINS=https://app.your-ui.com,https://api.your-domain.com
DEFAULT_MONGO_URI=mongodb://mongo:27017/node_engo_master
UPLOADS_PUBLIC_BASE=https://api.your-domain.com/uploads
```

> Commit only `.env.example` (no secrets); ignore the others in `.gitignore`.

---

# 2) Type-safe env loader (ESM + zod)

`src/config/env.mts`

```ts
import dotenvFlow from 'dotenv-flow';
import { z } from 'zod';

// Load .env.* based on APP_ENV (local|staging|production); falls back to 'local'
dotenvFlow.config({
  node_env: process.env.APP_ENV || 'local', // we use APP_ENV as the driver
  // path defaults to process.cwd()
  silent: true
});

const EnvSchema = z.object({
  APP_NAME: z.string().default('node-engo'),
  APP_ENV: z.enum(['local', 'staging', 'production']).default('local'),
  APP_PORT: z.coerce.number().int().positive().default(8089),

  // CORS comma-separated list
  CORS_ALLOWED_ORIGINS: z
    .string()
    .default('http://localhost:5173'),

  // Redis & Mongo (default master DB for multi-tenant bootstrap)
  REDIS_URL: z.string().url(),
  DEFAULT_MONGO_URI: z.string().min(1, 'DEFAULT_MONGO_URI is required'),

  // JWT / security
  JWT_SECRET: z.string().min(8, 'JWT_SECRET is too short'),

  // Static/public base for uploads (used in URL builders)
  UPLOADS_PUBLIC_BASE: z.string().url()
});

const parsed = EnvSchema.safeParse(process.env);
if (!parsed.success) {
  // Pretty error messages
  // eslint-disable-next-line no-console
  console.error('Invalid environment configuration:', parsed.error.format());
  // Exit early – fail fast
  process.exit(1);
}

const env = parsed.data;

// Export a normalized config object (frozen)
export const config = Object.freeze({
  APP_NAME: env.APP_NAME,
  APP_ENV: env.APP_ENV,
  APP_PORT: env.APP_PORT,
  CORS_ALLOWED_ORIGINS: env.CORS_ALLOWED_ORIGINS.split(',').map(s => s.trim()).filter(Boolean),

  REDIS_URL: env.REDIS_URL,
  DEFAULT_MONGO_URI: env.DEFAULT_MONGO_URI,

  JWT_SECRET: env.JWT_SECRET,
  UPLOADS_PUBLIC_BASE: env.UPLOADS_PUBLIC_BASE
});
```

> Anywhere in your app, import `config`:
>
> ```ts
> import { config } from '../config/env.mts';
> ```

---

# 3) TS config (strict ESM build)

`tsconfig.build.json`

```json
{
  "extends": "./tsconfig.json",
  "compilerOptions": {
    "outDir": "dist",
    "rootDir": "src",
    "module": "NodeNext",
    "moduleResolution": "NodeNext",
    "target": "ES2022",
    "lib": ["ES2022"],
    "strict": true,
    "noEmitOnError": true,
    "sourceMap": false,
    "declaration": false,
    "resolveJsonModule": true
  },
  "include": ["src"]
}
```

Make sure your root `tsconfig.json` has:

```json
{
  "compilerOptions": {
    "allowJs": false,
    "skipLibCheck": true,
    "esModuleInterop": true,
    "forceConsistentCasingInFileNames": true
  }
}
```

And your `package.json` has `"type": "module"`.

---

# 4) package.json scripts (dev/build/run per env)

Install small helpers:

```bash
npm i -D dotenv-flow cross-env tsx nodemon
npm i -D typescript @types/node
```

Add scripts:

```json
{
  "type": "module",
  "scripts": {
    // Development (watch) – runs TS directly with tsx
    "dev:local": "cross-env APP_ENV=local nodemon --watch src --ext mts,ts,js,json --exec tsx src/server.mts",
    "dev:staging": "cross-env APP_ENV=staging nodemon --watch src --ext mts,ts,js,json --exec tsx src/server.mts",
    "dev:prod": "cross-env APP_ENV=production nodemon --watch src --ext mts,ts,js,json --exec tsx src/server.mts",

    // Build once
    "build": "tsc -p tsconfig.build.json",

    // Start compiled server with env
    "start:local": "cross-env APP_ENV=local node dist/server.mjs",
    "start:staging": "cross-env APP_ENV=staging node dist/server.mjs",
    "start:prod": "cross-env APP_ENV=production node dist/server.mjs"
  }
}
```

> **Why this design?**
>
> * Single **build artifact** (`dist/`) for all envs.
> * **Environment is decided at runtime** via `APP_ENV`, and `dotenv-flow` loads the right `.env.*` automatically.
> * No duplication of code or builds.

---

# 5) Server usage (CORS etc.)

In `src/server.mts`, consume env like:

```ts
import express from 'express';
import cors from 'cors';
import { config } from './config/env.mts';
import { logger } from './helpers/logger.mts';

const app = express();

app.use(cors({
  origin: (origin, callback) => {
    if (!origin) return callback(null, true); // Postman / server-to-server
    if (config.CORS_ALLOWED_ORIGINS.includes(origin)) return callback(null, true);
    return callback(new Error(`CORS not allowed from: ${origin}`));
  },
  credentials: true
}));

// ... your middlewares, routes, etc.

app.listen(config.APP_PORT, () => {
  logger.info(`[${config.APP_ENV}] ${config.APP_NAME} listening on :${config.APP_PORT}`);
});
```

---

## How you’ll actually run it

**Local dev**

```bash
npm run dev:local
```

**Staging dev**

```bash
npm run dev:staging
```

**Build once**

```bash
npm run build
```

**Run compiled locally / staging / prod**

```bash
npm run start:local
npm run start:staging
npm run start:prod
```

---

## Optional: PM2 (staging/prod)

`ecosystem.config.cjs` (PM2 supports CJS best)

```js
module.exports = {
  apps: [
    {
      name: 'node-engo',
      script: 'dist/server.mjs',
      instances: 1,
      exec_mode: 'cluster',
      env_staging: {
        APP_ENV: 'staging'
      },
      env_production: {
        APP_ENV: 'production'
      }
    }
  ]
};
```

Deploy:

```bash
npm run build
pm2 start ecosystem.config.cjs --env production
```

---

## Why this is robust for multi-tenant Mongo

* **No env baked into the build** → the same `dist/` runs in any environment.
* Tenant DB selection still comes from your **runtime** (`req.user.mongoUri` from Redis + `authenticate`)—not from build-time.
* `dotenv-flow` respects `.env`, `.env.<APP_ENV>` layering, so you can keep shared keys in `.env` and override per environment cleanly.
