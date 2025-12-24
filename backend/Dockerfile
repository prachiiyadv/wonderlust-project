# Stage 1: Build & Test
FROM node:21 AS backend-builder

WORKDIR /app

# Copy only package files first (better caching)
COPY package*.json ./
RUN npm install

# Copy source code
COPY . .

# Run tests (optional – keep if you want CI validation)
RUN npm run test

# Stage 2: Production Image
FROM node:21-alpine

WORKDIR /app

# Copy built app from previous stage
COPY --from=backend-builder /app .

# Environment file
COPY .env.docker .env

# Expose backend port (MATCH docker-compose)
EXPOSE 8080

# Start application
CMD ["npm", "start"]
