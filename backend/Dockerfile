# Nestoria backend. Build context is the repo root so init-db.js can reach
# ../database/*.sql and scripts/ (wipe-seed etc.) ship alongside.
#   docker build -f backend/Dockerfile .
FROM node:22-alpine

ENV NODE_ENV=production \
    PORT=5000 \
    NODE_PATH=/app/backend/node_modules

WORKDIR /app/backend
COPY backend/package*.json ./
RUN npm ci --omit=dev && npm cache clean --force

COPY backend/ ./
COPY database/ /app/database/
COPY scripts/ /app/scripts/

USER node
EXPOSE 5000

HEALTHCHECK --interval=15s --timeout=5s --start-period=30s --retries=5 \
  CMD wget -qO- http://127.0.0.1:5000/api/health || exit 1

# Same as `npm start`, but exec so node gets signals directly.
CMD ["sh", "-c", "node init-db.js && exec node server.js"]
