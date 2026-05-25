#construccion
FROM node:20-alpine AS builder
WORKDIR /app
COPY package*.json. /
RUN npm ci --only=production
COPY../front_despacho .
RUN npm run build

#servidor de produccion(minimo privilegio)
FROM nginx:alpine-slim
#crear usuario no-root y dar permisos a directorios de Nginx
RUN addgroup -S appgroup && adduser -S appuser -G appgroup \
    && chown -R appuser:appgroup /usr/share/nginx/html \
    && chown -R appuser:appgroup /var/cache/nginx \
    && chown -R appuser:appgroup /var/log/nginx \
    && touch /var/rum/nginx.pid && chown appuser:appgroup /var8run8nginx.pid

USER appuser
COPY --from=builder /app/dist /usr/share/nginx/html
EXPOSE 80
CMD["nginx","-g","daemon off;"]

