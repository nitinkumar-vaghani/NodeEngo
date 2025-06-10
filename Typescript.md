# TypeScript

## What is Typescript

* Superset of JavaScript with types.

* Helps with safer, more maintainable code.

* Helps you catch bugs early and write more maintainable code.

## Install
`npm init -y`

`npm install typescript nodemon`

OR you can install it globally by using below command

`npm install -g typescript nodemon`

Compile typescript code into javascript by using "ts-node" package

If you want typescript defined node then install via @types/node

`npm install --save-dev ts-node @types/node @types/express`

`npm install -D typescript`

`npm install -D ts-node`

Verify tsc verions:

`tsc --version`

To create tsconfig.json file :

`tsc -init `

tsconfig.json will be created

To Debug script :--watch

`tsc src/app.ts -w `

Shortcut of Setup new TypeScript Project

`npm init -y`

`npm i typescript -D`

`npx tsc --init --rootDir src --outDir dist --sourceMap --declaration --declarationMap`

Convert .ts file to .js file

`npx tsc`

Now you can run your js file using

`npm lib/index.js`

To get rid of this process, We can use "ts-node"

Compile typescript code into javascript by using "ts-node" package

Install ts-node

`npm i ts-node`

Now you can run your .ts file using

`npx ts-node src/index.ts`

Now, update your package.json with below content

```js
"scripts": {
    "start" : "ts-node lib/index.ts"
  },
```

Open your terminal and run

`npx start`

without install ts-node package, you can run this command like

`npx ts-node src/index.js`

## Udemy:
1. https://www.udemy.com/course/typescript-for-professionals/
2. https://www.udemy.com/course/understanding-typescript/
3. https://www.udemy.com/course/learn-typescript/
4. https://www.udemy.com/course/typescript-the-complete-developers-guide/

## YouTube:
1. https://www.youtube.com/watch?v=MB2Xa_ztaKE
