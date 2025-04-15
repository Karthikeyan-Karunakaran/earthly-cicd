VERSION 0.8

# Base target: install dependencies
FROM node:18
WORKDIR /app

deps:
    COPY package.json package-lock.json ./
    RUN npm ci

# Build target (inherits from +base)
build:
    FROM +deps
    COPY . .
    RUN npm install --omit=dev
    # RUN npm test  # Uncomment this line if you have tests

# Docker target: creates and saves image
docker:
    FROM +build
    ENTRYPOINT ["node", "index.js"]
    SAVE IMAGE karthikeyank2004/myapp:latest
