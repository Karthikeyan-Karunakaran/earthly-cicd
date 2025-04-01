VERSION 0.7

# Build Stage
FROM node:18-alpine AS build

WORKDIR /app
COPY . .

RUN --mount=type=cache,target=/root/.cache \
    node server.js & sleep 2 && pkill node

# Final Stage: Create a minimal container
FROM alpine:latest

WORKDIR /app
COPY --from=build /app/server.js .

CMD ["sh", "-c", "node /app/server.js"]

# Build Docker Image
TARGET +docker
FROM node:18-alpine
WORKDIR /app
COPY . .
RUN --mount=type=cache,target=/root/.cache node server.js & sleep 2 && pkill node
ENTRYPOINT ["node", "server.js"]

