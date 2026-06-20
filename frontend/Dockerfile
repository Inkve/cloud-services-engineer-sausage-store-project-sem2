# Build
FROM node:10-alpine AS builder

WORKDIR /app
COPY package*.json ./

RUN npm ci --no-audit --no-fund && \
    npm cache clean --force

COPY . .
RUN npm run build -- --prod

# Release
FROM nginx:1.31.1-alpine-slim

# Fix CVE-2026-45447
RUN apk add --no-cache \
    openssl=3.5.7-r0

COPY --from=builder /app/dist/frontend /usr/share/nginx/html

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]