# -------------------------------
# Base Image
# -------------------------------
FROM node:18-alpine AS base

WORKDIR /app

# Install dumb-init to handle process signals correctly
RUN apk add --no-cache dumb-init

# -------------------------------
# Dependencies
# -------------------------------
FROM base AS deps

# Copy only package files first to leverage Docker cache
COPY package*.json ./

# Install ONLY production dependencies
# (Use 'npm ci' for reliable builds based on package-lock.json)
RUN npm ci --only=production

# -------------------------------
# Runtime
# -------------------------------
FROM base AS runner

ENV NODE_ENV=production
ENV PORT=3000

# Create the user and group
RUN addgroup -S app && adduser -S app -G app

WORKDIR /app

# 1. Copy node_modules from the deps stage
# We use --chown here to avoid a slow "RUN chown" command later
COPY --from=deps --chown=app:app /app/node_modules ./node_modules

# 2. Copy the rest of the application code
COPY --chown=app:app . .

# Switch to the non-root user
USER app

EXPOSE 3000

ENTRYPOINT ["dumb-init", "--"]
CMD ["node", "server.js"]