FROM node:20-slim AS builder

WORKDIR /app

# Install OpenSSL for Prisma (required for Prisma binaries)
RUN apt-get update -y && apt-get install -y openssl && rm -rf /var/lib/apt/lists/*

# Copy package files
COPY package*.json ./
COPY package-lock.json* ./
COPY tsconfig.json ./
COPY prisma ./prisma

# Install ALL dependencies (including devDependencies for build)
RUN npm ci

# Generate Prisma Client
RUN npx prisma generate

# Copy source code
COPY src ./src

# Build TypeScript to JavaScript
RUN npm run build

# Verify build output exists
RUN ls -la dist/ && test -f dist/index.js || (echo "ERROR: dist/index.js not found!" && exit 1)

# Production stage
FROM node:20-slim AS production

WORKDIR /app

# Install OpenSSL for Prisma runtime
RUN apt-get update -y && apt-get install -y openssl && rm -rf /var/lib/apt/lists/*

# Copy package files
COPY package*.json ./
COPY package-lock.json* ./

# Install only production dependencies
RUN npm ci --only=production

# Copy Prisma schema and generated client
COPY prisma ./prisma
COPY --from=builder /app/node_modules/.prisma ./node_modules/.prisma
COPY --from=builder /app/node_modules/@prisma ./node_modules/@prisma

# Copy built files
COPY --from=builder /app/dist ./dist

# Expose port
EXPOSE 3000

# Run migrations and start the application
CMD ["sh", "-c", "echo '=== Container Started ===' && npx prisma migrate deploy && echo '=== Starting Node ===' && node dist/index.js"]

