VERSION 0.7

# Define Docker Build Target
TARGET +docker
FROM node:18-alpine

WORKDIR /app
COPY . .

# Use cache to speed up builds
RUN --mount=type=cache,target=/root/.cache \
    node server.js & sleep 2 && pkill node

# Set entrypoint
ENTRYPOINT ["node", "server.js"]
