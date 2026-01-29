# ---------- STAGE 1: BUILD ----------
# Используем образ с SDK для сборки приложения
FROM mcr.microsoft.com/dotnet/sdk:8.0 AS build

WORKDIR /app

# Копируем проект и восстанавливаем зависимости
COPY *.csproj ./
RUN dotnet restore

# Копируем все файлы проекта
COPY . ./

# Публикуем приложение в папку /app/publish (Release, self-contained false)
RUN dotnet publish -c Release -o /app/publish

# ---------- STAGE 2: RUNTIME ----------
# Используем образ только с ASP.NET runtime
FROM mcr.microsoft.com/dotnet/aspnet:8.0 AS final

WORKDIR /app

# Копируем из стадии сборки только публикацию приложения
COPY --from=build /app/publish ./

# Указываем порт приложения
EXPOSE 5000

# Команда запуска приложения
ENTRYPOINT ["dotnet", "my-app.dll"]

