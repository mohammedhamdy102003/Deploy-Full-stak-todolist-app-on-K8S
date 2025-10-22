&nbsp;&nbsp;&nbsp;&nbsp; <img height="200" src="https://raw.githubusercontent.com/github/explore/80688e429a7d4ef2fca1e82350fe8e3517d3494d/topics/typescript/typescript.png"/> </div>
:white_check_mark: To-do App

A To-do App with MongoDB
, Express
, React
, Node.js
, and TypeScript
.

:star: Source

How to Build a Todo App with React, TypeScript, NodeJS, and MongoDB
 by Ibrahima Ndaw
 on freeCodeCamp.org

:computer: Server-side
Structure of the project
├── server
    ├── dist
    ├── node_modules
    ├── src
        ├── controllers
        │   └── todos
        │       └── index.ts
        ├── models
        │   └── todo.ts
        ├── routes
        │   └── index.ts
        └── types
            └── todo.ts
        ├── app.ts
    ├── nodemon.json
    ├── package.json
    ├── tsconfig.json

TypeScript Configuration
tsc --init


Replace the default tsconfig.json with:

{
  "compilerOptions": {
    "target": "es6",
    "module": "commonjs",
    "outDir": "dist/js",
    "rootDir": "src",
    "strict": true,
    "esModuleInterop": true,
    "forceConsistentCasingInFileNames": true
  },
  "include": ["src/**/*"],
  "exclude": ["src/types/*.ts", "node_modules", ".vscode"]
}

Install Dependencies
yarn add typescript -g
yarn add express cors mongoose
yarn add -D @types/node @types/express @types/mongoose @types/cors concurrently nodemon


Update package.json scripts:

"scripts": {
  "build": "tsc",
  "start": "concurrently \"tsc -w\" \"nodemon dist/js/app.js\""
}

nodemon.json (for environment variables)
{
    "env": {
        "MONGO_USER": "your-username",
        "MONGO_PASSWORD": "your-password",
        "MONGO_DB": "your-db-name"
    }
}


Add nodemon.json to .gitignore to protect DB credentials.

app.ts (Server Entry)
import express, { Express } from 'express'
import mongoose from 'mongoose'
import cors from 'cors'
import todoRoutes from './routes'

const app: Express = express()
const PORT: string | number = process.env.PORT || 4000

app.use(cors())
app.use(todoRoutes)

const uri: string = `mongodb+srv://${process.env.MONGO_USER}:${process.env.MONGO_PASSWORD}@cluster0.xo006.mongodb.net/${process.env.MONGO_DB}?retryWrites=true&w=majority`
const options = { useNewUrlParser: true, useUnifiedTopology: true }
mongoose.set('useFindAndModify', false)

mongoose
.connect(uri, options)
.then(() =>
    app.listen(PORT, () =>
        console.log(`Server running on http://localhost:${PORT}`)
    )
)
.catch((error) => {
    throw error
})

:computer: Client-side (React + TypeScript)
Setup
npx create-react-app client --template typescript
cd client
yarn add axios

Project Structure
├── client
    ├── node_modules
    ├── public
    ├── src
    │   ├── components
    │   │   ├── AddTodo.tsx
    │   │   └── TodoItem.tsx
    │   ├── API.ts
    │   ├── App.tsx
    │   ├── index.tsx
    │   └── type.d.ts
    ├── tsconfig.json
    ├── package.json
    └── yarn.lock

:whale: Dockerization
Server Dockerfile (server/Dockerfile)
FROM node:16

WORKDIR /usr/src/app

COPY package*.json ./

RUN npm install ts-node typescript @types/node @types/express @types/cors @types/mongoose --save-dev && npm install

COPY . .

EXPOSE 5001

CMD ["npx", "ts-node", "src/app.ts"]

Client Dockerfile (client/Dockerfile)
FROM node:16

WORKDIR /usr/src/app

COPY package*.json ./

RUN npm install

COPY . .

RUN npm run build
RUN npm install -g serve

EXPOSE 3002

CMD ["serve", "-s", "build"]

docker-compose.yml
version: '3.8'

services:
  mongo:
    image: mongo
    container_name: todo-mongo
    ports:
      - "27017:27017"
    volumes:
      - mongo_data:/data/db

  server:
    build: ./server
    container_name: todo-server
    ports:
      - "5001:5000"
    environment:
      - MONGO_URL=mongodb://mongo:27017/todoapp
    depends_on:
      - mongo

  client:
    build: ./client
    container_name: todo-client
    ports:
      - "3002:3000"
    environment:
      - REACT_APP_API_URL=http://localhost:5001
    depends_on:
      - server

volumes:
  mongo_data:

Run with Docker
docker-compose up -d --build
docker ps


Access:

Frontend: http://<IP_MACHINE>:3002

Backend API: http://<IP_MACHINE>:5001

:rocket: Run Locally without Docker

Server

cd server
yarn install
yarn start


Client

cd client
yarn install
yarn start
