# -------------------------------
# Base Image
# -------------------------------
FROM node:18-alpine AS base

WORKDIR /app

RUN apk add --no-cache dumb-init

# -------------------------------
# Dependencies
# -------------------------------
FROM base AS deps
COPY package*.json ./
RUN npm ci --only=production

# -------------------------------
# Runtime
# -------------------------------
FROM base AS runner

ENV NODE_ENV=production
ENV PORT=3000

RUN addgroup -S app && adduser -S app -G app

WORKDIR /app

COPY --from=deps /app/node_modules ./node_modules
COPY . .

RUN chown -R app:app /app
USER app

EXPOSE 3000

ENTRYPOINT ["dumb-init", "--"]
CMD ["node", "server.js"]
