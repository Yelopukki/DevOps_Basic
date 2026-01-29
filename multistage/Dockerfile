# ---------- STAGE 1: BUILD ----------
FROM node:18 AS build

WORKDIR /app

# Кешируем зависимости
COPY package.json ./
RUN npm install

# Копируем исходники
COPY . .

# Собираем статику → /app/dist
RUN npm run build


# ---------- STAGE 2: RUNTIME ----------
FROM nginx:alpine

# Копируем ТОЛЬКО результат сборки
COPY --from=build /app/dist /usr/share/nginx/html

EXPOSE 80

