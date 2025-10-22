# Use Node.js 16
FROM node:16

# Set working directory
WORKDIR /usr/src/app

# Copy package.json and package-lock.json first
COPY package*.json ./

# Install all dependencies
RUN npm install

# Copy the rest of the project files
COPY . .

# Expose the port the app uses
EXPOSE 5001

# Start the server using ts-node
CMD ["npx", "ts-node", "src/app.ts"]


