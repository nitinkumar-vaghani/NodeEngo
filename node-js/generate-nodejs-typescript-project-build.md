Since you’ve already created a `src/config/env.mts` file to manage environment variables and imported it into `src/server.mts` with `import { config } from "./config/env.mts";`, you’re on a good path for handling environment variables in a structured way. Your `env.mts` file uses `dotenv` to load environment variables and provides a `config` object with type-safe access, default values, and required field validation. This setup is cleaner than directly calling `dotenv.config()` in `src/server.mts`, as it centralizes environment variable management.

Given that your "Node Engo" application uses Node.js, Express, strict TypeScript (100% ESM), MongoDB with a multi-tenant model, and emphasizes performance, reliability, and clean code, you don’t need to follow all the steps from the previous response exactly. However, some steps are still relevant to adapt your existing setup for environment-specific builds (local, staging, production). The key difference is that you’re already loading environment variables via `src/config/env.mts`, so we’ll leverage that instead of modifying `src/server.mts` to call `dotenv.config()` directly.

Below, I’ll guide you step-by-step to generate environment-specific builds for your Node Engo backend application, ensuring that the appropriate `.env` file (`.env.local`, `.env.staging`, `.env.production`) is loaded based on the environment. I’ll also integrate with your existing `src/config/env.mts` and ensure compatibility with your project’s standards.

### Key Points from Your Setup
- **Existing `env.mts`**: Loads `.env` using `dotenv` and defines a `config` object with required and optional variables.
- **`.env` File**: Your provided `.env` contains variables like `APP_ENV`, `MONGO_DB_BASE_URI`, etc., which are read by `src/config/env.mts`.
- **Goal**: Create builds for local, staging, and production environments, copying the appropriate `.env` file (e.g., `.env.local`, `.env.staging`, `.env.production`) to the build output (`dist`) so that `src/config/env.mts` loads the correct environment variables at runtime.
- **Current Issue**: Your `env.mts` loads `.env` from the project root, but for builds, you need to ensure the correct environment-specific `.env` file is copied to `dist` and loaded by `dotenv`.

### Step-by-Step Solution to Generate Environment-Based Builds

#### 1. Create Environment-Specific `.env` Files
Since you’re already using `.env` for local development (as shown in `env.txt`), create separate `.env` files for each environment to manage variables like database URIs, API keys, and ports. This ensures environment-specific configurations are loaded at runtime without hardcoding.

- **In the project root**, create:
  - `.env.local` (copy your existing `.env` content for local development).
  - `.env.staging` (for staging environment, update values like `APP_ENV`, `MONGO_DB_BASE_URI`, etc.).
  - `.env.production` (for production, with production-specific values).

- **Example `.env.local`** (based on your provided `.env`):
  ```
  # APP Config
  APP_NAME="NodeEngo"
  APP_ENV=local
  APP_MODE=debug
  APP_ENABLE_LOG=true
  APP_PORT=8089
  APP_URL=http://nv.api-nod-engo.com:8089

  # MongoDB Config
  MONGO_DB_BASE_URI=mongodb://localhost:27017/
  MONGO_DB_DEFAULT_NAME=node_engo
  MONGO_DB_PREFIX=cc_

  # Redis Config
  REDIS_URL=redis://localhost:6379

  # Rate Limit Config
  RATE_LIMIT_LOGIN_LOCKING_PERIOD=1
  RATE_LIMIT_LOGIN_MAX_REQUEST=60
  RATE_LIMIT_REFRESH_TOKEN_LOCKING_PERIOD=1
  RATE_LIMIT_REFRESH_TOKEN_MAX_REQUEST=3
  RATE_LIMIT_LOCKING_PERIOD=1
  RATE_LIMIT_MAX_REQUEST=100

  # 2FA
  TWOFA_TOKEN_EXPIRY_TIME=10
  AUTH_TOKEN_EXPIRY_TIME=1440

  # CORS
  CORS_ALLOWED_ORIGINS=http://localhost:5173,http://127.0.0.1:5173,http://localhost:5174,http://127.0.0.1:5174,http://dev.nodeengo.com

  # FILE
  FILE_SIZE_LIMIT_MB=2
  CSV_FILE_MAX_SIZE_MB=2
  UPLOAD_BASE_PATH=uploads
  ```

- **Example `.env.staging`**:
  ```
  # APP Config
  APP_NAME="NodeEngo"
  APP_ENV=staging
  APP_MODE=warning
  APP_ENABLE_LOG=true
  APP_PORT=8089
  APP_URL=http://staging.api-node-engo.com:8089

  # MongoDB Config
  MONGO_DB_BASE_URI=mongodb://localhost:27017/
  MONGO_DB_DEFAULT_NAME=node_engo
  MONGO_DB_PREFIX=cc_

  # Redis Config
  REDIS_URL=redis://localhost:6379

  # Rate Limit Config
  RATE_LIMIT_LOGIN_LOCKING_PERIOD=1
  RATE_LIMIT_LOGIN_MAX_REQUEST=60
  RATE_LIMIT_REFRESH_TOKEN_LOCKING_PERIOD=1
  RATE_LIMIT_REFRESH_TOKEN_MAX_REQUEST=3
  RATE_LIMIT_LOCKING_PERIOD=1
  RATE_LIMIT_MAX_REQUEST=100

  # 2FA
  TWOFA_TOKEN_EXPIRY_TIME=10
  AUTH_TOKEN_EXPIRY_TIME=1440

  # CORS
  CORS_ALLOWED_ORIGINS=http://staging.nodeengo.com

  # FILE
  FILE_SIZE_LIMIT_MB=2
  CSV_FILE_MAX_SIZE_MB=2
  UPLOAD_BASE_PATH=uploads
  ```

- **Example `.env.production`**:
  ```
   # APP Config
  APP_NAME="NodeEngo"
  APP_ENV=production
  APP_MODE=error
  APP_ENABLE_LOG=false
  APP_PORT=8089
  APP_URL=http://api-nodengo.com

  # MongoDB Config
  MONGO_DB_BASE_URI=mongodb://localhost:27017/
  MONGO_DB_DEFAULT_NAME=node_engo
  MONGO_DB_PREFIX=cc_

  # Redis Config
  REDIS_URL=redis://localhost:6379

  # Rate Limit Config
  RATE_LIMIT_LOGIN_LOCKING_PERIOD=1
  RATE_LIMIT_LOGIN_MAX_REQUEST=60
  RATE_LIMIT_REFRESH_TOKEN_LOCKING_PERIOD=1
  RATE_LIMIT_REFRESH_TOKEN_MAX_REQUEST=3
  RATE_LIMIT_LOCKING_PERIOD=1
  RATE_LIMIT_MAX_REQUEST=100

  # 2FA
  TWOFA_TOKEN_EXPIRY_TIME=10
  AUTH_TOKEN_EXPIRY_TIME=1440

  # CORS
  CORS_ALLOWED_ORIGINS=http://staging.nodeengo.com

  # FILE
  FILE_SIZE_LIMIT_MB=2
  CSV_FILE_MAX_SIZE_MB=2
  UPLOAD_BASE_PATH=uploads
  ```

- **Update `.gitignore`**:
  Ensure all `.env` files are ignored to prevent committing sensitive data:
  ```
  .env
  .env.local
  .env.staging
  .env.production
  ```
  Optionally, add a `.env.example` with placeholder values for reference:
  ```
  APP_NAME="Node Engo"
  APP_ENV=local
  # ... other variables with placeholder values
  ```

#### 2. Update `tsconfig.json` for Building
Your `tsconfig.json` currently has `"noEmit": true`, which prevents generating compiled JavaScript files. For building, you need to set `"noEmit": false` and specify an output directory (`dist`). Since you’re using ESM, the compiled files will be `.js` but treated as ESM modules.

- Updated `tsconfig.json`:
  ```json
  {
    "compilerOptions": {
      "target": "ES2022",
      "module": "ESNext",
      "moduleResolution": "Bundler",
      "lib": ["ES2022"],
      "moduleDetection": "force",
      "baseUrl": "./src",
      "strict": true,
      "esModuleInterop": true,
      "skipLibCheck": true,
      "resolveJsonModule": true,
      "allowImportingTsExtensions": true,
      "noEmit": false,
      "outDir": "./dist",
      "declaration": true, // Optional: Generate .d.ts files
      "types": ["node", "express"],
      "forceConsistentCasingInFileNames": true,
      "allowSyntheticDefaultImports": true
    },
    "include": ["src/**/*.mts", "src/**/*.d.ts"]
  }
  ```

#### 3. Update `src/config/env.mts` for Environment-Specific Loading
Your `env.mts` loads `.env` via `dotenv.config()`. To support environment-specific `.env` files, modify it to load `.env.local`, `.env.staging`, or `.env.production` based on `NODE_ENV`. Use `dotenv`’s `path` option to specify the file.

- Updated `src/config/env.mts`:
  ```typescript
  import dotenv from "dotenv";
  import { z } from "zod";
  import path from "path";

  // Determine which .env file to load based on NODE_ENV
  const envFileMap: Record<string, string> = {
    local: ".env.local",
    staging: ".env.staging",
    production: ".env.production",
  };

  const nodeEnv = process.env.NODE_ENV || "local";
  const envFile = envFileMap[nodeEnv] || ".env.local";
  dotenv.config({ path: path.resolve(process.cwd(), envFile) });

  // Validation schema for environment variables
  const envSchema = z.object({
    APP_NAME: z.string().min(1),
    APP_ENV: z.enum(["local", "staging", "production"]).default("local"),
    APP_MODE: z.enum(["error", "warning", "debug"]).default("error"),
    APP_ENABLE_LOG: z.string().transform(val => val === "true").default("false"),
    APP_SLUG: z.string().min(1),
    APP_PORT: z.string().transform(Number).default("8000"),
    APP_URL: z.string().url(),
    MONGO_DB_BASE_URI: z.string().url(),
    MONGO_DB_DEFAULT_NAME: z.string().min(1),
    MONGO_DB_PREFIX: z.string().default("cc_"),
    REDIS_URL: z.string().url(),
    RATE_LIMIT_LOGIN_LOCKING_PERIOD: z.string().transform(Number).default("10"),
    RATE_LIMIT_LOGIN_MAX_REQUEST: z.string().transform(Number).default("10"),
    RATE_LIMIT_REFRESH_TOKEN_LOCKING_PERIOD: z.string().transform(Number).default("1"),
    RATE_LIMIT_REFRESH_TOKEN_MAX_REQUEST: z.string().transform(Number).default("3"),
    RATE_LIMIT_LOCKING_PERIOD: z.string().transform(Number).default("1"),
    RATE_LIMIT_MAX_REQUEST: z.string().transform(Number).default("100"),
    TWOFA_TOKEN_EXPIRY_TIME: z.string().transform(Number).default("5"),
    AUTH_TOKEN_EXPIRY_TIME: z.string().transform(Number).default("25"),
    FILE_SIZE_LIMIT_MB: z.string().transform(Number).default("2"),
    CSV_FILE_MAX_SIZE_MB: z.string().transform(Number).default("2"),
    UPLOAD_BASE_PATH: z.string().default("uploads"),
    CORS_ALLOWED_ORIGINS: z.string().transform(val => val.split(",").map(origin => origin.trim()).filter(Boolean)).default(""),
  });

  // Parse and validate environment variables
  const parsedEnv = envSchema.parse(process.env);

  export const config = parsedEnv;
  ```

##### Changes
- **Dynamic `.env` Loading**: Loads `.env.local`, `.env.staging`, or `.env.production` based on `NODE_ENV`.
- **Zod Validation**: Replaced `required` function with a Zod schema for type-safe validation, default values, and transformations (e.g., string to number for `APP_PORT`).
- **Error Handling**: Throws descriptive errors if required variables are missing or invalid.
- **CORS Handling**: Kept the `split` logic for `CORS_ALLOWED_ORIGINS` but integrated it into Zod.

#### 4. Update `package.json` Scripts
Add build scripts for each environment. The build process compiles TypeScript to JavaScript (`dist`) and copies the environment-specific `.env` file to `dist`. Use `cross-env` for setting `NODE_ENV` and `cpy-cli` for copying files.

- Install dependencies:
  ```bash
  npm install --save-dev cross-env cpy-cli
  ```

- Updated `package.json`:
  ```json
  {
    "name": "node_engo",
    "version": "1.0.0",
    "description": "",
    "main": "index.js",
    "type": "module",
    "scripts": {
      "dev": "nodemon",
      "build": "tsc",
      "build:local": "cross-env NODE_ENV=local npm run build && cpy .env.local dist --rename=.env",
      "build:staging": "cross-env NODE_ENV=staging npm run build && cpy .env.staging dist --rename=.env",
      "build:production": "cross-env NODE_ENV=production npm run build && cpy .env.production dist --rename=.env",
      "start": "node dist/server.js",
      "process-batches": "tsx src/scripts/process-node-engo-batches.mts"
    },
    "dependencies": {
      "@azure/storage-blob": "^12.28.0",
      "axios": "^1.10.0",
      "chalk": "^5.4.1",
      "cors": "^2.8.5",
      "crypto": "^1.0.1",
      "csv-parser": "^3.2.0",
      "dotenv": "^16.5.0",
      "express": "^5.1.0",
      "express-rate-limit": "^7.5.1",
      "mongoose": "^8.16.0",
      "multer": "^2.0.1",
      "rate-limit-redis": "^4.2.1",
      "redis": "^5.5.6",
      "uuid": "^11.1.0",
      "zod": "^3.25.67"
    },
    "devDependencies": {
      "@types/cors": "^2.8.19",
      "@types/express": "^5.0.3",
      "@types/multer": "^2.0.0",
      "@types/node": "^24.0.3",
      "cross-env": "^7.0.3",
      "cpy-cli": "^5.0.0",
      "nodemon": "^3.1.10",
      "tsx": "^4.20.3",
      "typescript": "^5.8.3"
    }
  }
  ```

##### Explanation
- **Scripts**:
  - `build`: Compiles TypeScript to JavaScript in `dist` (e.g., `src/server.mts` → `dist/server.js`).
  - `build:<env>`: Sets `NODE_ENV`, runs `tsc`, and copies the environment-specific `.env` file to `dist/.env`.
  - `start`: Runs the built app, loading `dist/.env` via `dotenv` in `src/config/env.mts`.
- **Dependencies**: Added `cross-env` and `cpy-cli` for environment setting and file copying.

#### 5. Update `src/server.mts` (Minimal Change)
Since you’re already importing `config` from `src/config/env.mts`, no changes are needed in `src/server.mts` unless you want to use `config.APP_ENV` for environment-specific logic (e.g., logging levels). Example:

```typescript
import express from "express";
import { config } from "./config/env.mts";
import { logger } from "./logs/logger.mts";

const app = express();

// Environment-specific logic
if (config.APP_ENV === "production") {
  logger.level = "error"; // Reduce logging in production
} else {
  logger.level = "debug";
}

// ... rest of your server setup

app.listen(config.APP_PORT, () => {
  logger.info(`Server running on ${config.APP_URL} in ${config.APP_ENV} mode`);
});
```

#### 6. Build and Deploy Process
- **Local Environment**:
  - Run: `npm run build:local`
  - Output: `./dist` with compiled JS files and `.env` copied from `.env.local`.
  - Run: `npm start` (loads `dist/.env`).
  - Test locally: `http://nv.api-node-engo.com:8089`.

- **Staging Environment**:
  - Run: `npm run build:staging`
  - Output: `./dist` with compiled JS and `.env` from `.env.staging`.
  - Deploy: Copy `dist` to staging server (e.g., via SCP or CI/CD).
    ```bash
    scp -r dist user@staging-server:/path/to/app
    ```
  - On staging server:
    ```bash
    cd /path/to/app
    npm ci --production
    npm start
    ```
  - Ensure MongoDB/Redis are configured per `.env.staging`.

- **Production Environment**:
  - Run: `npm run build:production`
  - Output: `./dist` with compiled JS and `.env` from `.env.production`.
  - Deploy: Copy `dist` to production server.
  - On production server:
    ```bash
    cd /path/to/app
    npm ci --production
    pm2 start npm --name "node-engo" -- start
    ```
  - Use PM2 for process management: `pm2 logs`, `pm2 monit`.

#### 7. CI/CD Integration (Optional)
Automate builds and deployments with GitHub Actions. Example workflow (`.github/workflows/deploy.yml`):

```yaml
name: Deploy Node Engo
on:
  push:
    branches: [main, staging]
jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-node@v3
        with:
          node-version: '20'
      - name: Install dependencies
        run: npm ci
      - name: Build
        run: npm run build:${{ github.ref_name == 'main' && 'production' || 'staging' }}
      - name: Deploy to server
        env:
          DEPLOY_SERVER: ${{ github.ref_name == 'main' && 'prod-server' || 'staging-server' }}
          DEPLOY_USER: ${{ secrets.DEPLOY_USER }}
          DEPLOY_PATH: ${{ github.ref_name == 'main' && '/path/to/prod' || '/path/to/staging' }}
        run: |
          rsync -avz --delete ./dist/ $DEPLOY_USER@$DEPLOY_SERVER:$DEPLOY_PATH
      - name: Restart server
        env:
          DEPLOY_SERVER: ${{ github.ref_name == 'main' && 'prod-server' || 'staging-server' }}
          DEPLOY_USER: ${{ secrets.DEPLOY_USER }}
        run: ssh $DEPLOY_USER@$DEPLOY_SERVER "cd $DEPLOY_PATH && npm ci --production && pm2 restart node-engo"
```

- Store secrets (e.g., `DEPLOY_USER`, server IPs) in GitHub Secrets.
- Adjust `DEPLOY_SERVER` and `DEPLOY_PATH` for your servers.

#### 8. Best Practices for Performance, Reliability, and Clean Code
- **Performance**:
  - Use ESM for modern Node.js performance.
  - Add clustering in `src/server.mts` for production:
    ```typescript
    import cluster from "cluster";
    import os from "os";
    if (config.APP_ENV === "production" && cluster.isPrimary) {
      const numCPUs = os.cpus().length;
      for (let i = 0; i < numCPUs; i++) {
        cluster.fork();
      }
    } else {
      // Your app.listen code
    }
    ```
- **Reliability**:
  - Use PM2 in production for auto-restarts: `pm2 start npm --name "node-engo" -- start`.
  - Add a health check endpoint:
    ```typescript
    app.get("/health", (req, res) => sendResponse(res, "Server is healthy", { status: "ok" }, true, 200));
    ```
- **Clean Code**:
  - Centralized env management in `src/config/env.mts` with Zod ensures type safety.
  - Avoid hardcoding env-specific logic outside `config`.
- **Security**:
  - Never commit `.env*` files.
  - Use secrets management (e.g., AWS Secrets Manager) for production if possible.

#### 9. Testing the Build
- **Local**:
  ```bash
  npm run build:local
  npm start
  curl http://nv.api-node-engo.com:8089/health
  ```
- **Staging**:
  - Build and deploy to staging server, verify `config.APP_ENV === "staging"`.
- **Production**:
  - Build and deploy, use PM2 logs to confirm.

#### Why You Don’t Need All Previous Steps
- **Existing `env.mts`**: Your `src/config/env.mts` already handles env loading, so you don’t need to add `dotenv.config()` to `src/server.mts`.
- **Zod Validation**: The updated `env.mts` with Zod is more robust than the previous `required` function.
- **Dynamic `.env` Loading**: The `envFileMap` approach in `env.mts` replaces the need to modify `server.mts` for env loading.

This solution leverages your existing `src/config/env.mts`, ensures environment-specific builds, and aligns with your project’s standards.
