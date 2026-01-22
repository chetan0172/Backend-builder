# -------------------------------
# Base Image
# -------------------------------
FROM node:18-alpine AS base

WORKDIR /app

# Install OS dependencies
RUN apk add --no-cache dumb-init

# -------------------------------
# Dependencies Stage
# -------------------------------
FROM base AS deps

COPY backend/package*.json ./
RUN npm install --production

# -------------------------------
# Production Stage
# -------------------------------
FROM base AS runner

ENV NODE_ENV=production
ENV PORT=3000

# Create non-root user
RUN addgroup -S app && adduser -S app -G app

WORKDIR /app

# Copy dependencies
COPY --from=deps /app/node_modules ./node_modules
COPY backend/ ./

# Ownership
RUN chown -R app:app /app

USER app

EXPOSE 3000

# Use dumb-init to avoid zombie processes
ENTRYPOINT ["dumb-init", "--"]

CMD ["node", "server.js"]
