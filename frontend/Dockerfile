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

RUN addgroup -S nginx_user && \
    adduser -S -s /sbin/nologin nginx_user -G nginx_user

COPY --from=builder /app/dist/frontend /usr/share/nginx/html

RUN chown -R nginx_user:nginx_user /var/cache/nginx && \
    chown -R nginx_user:nginx_user /var/log/nginx && \
    chown -R nginx_user:nginx_user /etc/nginx/conf.d && \
    touch /var/run/nginx.pid && \
    chown -R nginx_user:nginx_user /var/run/nginx.pid

USER nginx_user

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]