# ---------- STAGE 1: BUILD ----------
FROM node:18 AS build

WORKDIR /app
COPY package.json ./
RUN npm install
COPY . .
RUN npm run build

# ---------- STAGE 2: RUNTIME ----------
FROM nginx:alpine

RUN apk add --no-cache curl

# Создаем пользователя и группу
RUN addgroup -S appgroup && adduser -S appuser -G appgroup

# Папки для кеша и run
RUN mkdir -p /home/appuser/cache/client_temp \
             /home/appuser/cache/proxy_temp \
             /home/appuser/cache/fastcgi_temp \
             /home/appuser/cache/uwsgi_temp \
             /home/appuser/cache/scgi_temp \
             /home/appuser/run \
    && chown -R appuser:appgroup /home/appuser

# Копируем фронтенд
COPY --from=build /app/dist /usr/share/nginx/html
RUN chown -R appuser:appgroup /usr/share/nginx/html

# Копируем основной конфиг
COPY nginx-nonroot.conf /home/appuser/nginx.conf
RUN chown appuser:appgroup /home/appuser/nginx.conf

# Используем пользователя
USER appuser

EXPOSE 8080

HEALTHCHECK --interval=10s --timeout=5s --start-period=5s --retries=3 \
    CMD curl --silent --fail http://localhost:8080/ || exit 1
# Запуск Nginx с указанием собственного конфига
CMD ["nginx", "-g", "daemon off;", "-c", "/home/appuser/nginx.conf"]

