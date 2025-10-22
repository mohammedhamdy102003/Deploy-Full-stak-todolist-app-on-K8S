# Use Node.js 16
FROM node:16

WORKDIR /usr/src/app

COPY package*.json ./

RUN npm install

COPY . .

# Build React app
RUN npm run build

# Use a lightweight server to serve React build
RUN npm install -g serve

EXPOSE 3002

CMD ["serve", "-s", "build", "-l", "3000"]

