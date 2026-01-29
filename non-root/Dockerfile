FROM node:18 AS build

WORKDIR /app
COPY package.json ./
RUN npm install
COPY . .
RUN npm run build



FROM nginx:alpine

RUN apk add --no-cache curl

RUN addgroup -S appgroup && adduser -S appuser -G appgroup

RUN mkdir -p /home/appuser/cache/client_temp \
             /home/appuser/cache/proxy_temp \
             /home/appuser/cache/fastcgi_temp \
             /home/appuser/cache/uwsgi_temp \
             /home/appuser/cache/scgi_temp \
             /home/appuser/run \
    && chown -R appuser:appgroup /home/appuser

COPY --from=build /app/dist /usr/share/nginx/html
RUN chown -R appuser:appgroup /usr/share/nginx/html

COPY nginx-nonroot.conf /home/appuser/nginx.conf
RUN chown appuser:appgroup /home/appuser/nginx.conf

USER appuser

EXPOSE 8080

HEALTHCHECK --interval=10s --timeout=5s --start-period=5s --retries=3 \
    CMD curl --silent --fail http://localhost:8080/ || exit 1

CMD ["nginx", "-g", "daemon off;", "-c", "/home/appuser/nginx.conf"]

