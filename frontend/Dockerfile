# Nestoria frontend: Vite build served by nginx, with /api proxied to the
# backend so the whole app lives on one origin.
#   docker build -f frontend/Dockerfile .
FROM node:22-alpine AS build

WORKDIR /app
COPY frontend/package*.json ./
RUN npm ci

COPY frontend/ ./
# Relative by default: nginx below forwards /api to the backend.
ARG VITE_API_URL=/api
ARG VITE_GOOGLE_CLIENT_ID=
ENV VITE_API_URL=$VITE_API_URL \
    VITE_GOOGLE_CLIENT_ID=$VITE_GOOGLE_CLIENT_ID
RUN npm run build

FROM nginx:1.27-alpine

# Rendered to /etc/nginx/conf.d/default.conf by the image's envsubst entrypoint.
ENV API_UPSTREAM=http://backend:5000
COPY frontend/nginx/default.conf.template /etc/nginx/templates/default.conf.template
COPY --from=build /app/build /usr/share/nginx/html

EXPOSE 80

HEALTHCHECK --interval=15s --timeout=5s --start-period=10s --retries=5 \
  CMD wget -qO- http://127.0.0.1/ >/dev/null || exit 1
