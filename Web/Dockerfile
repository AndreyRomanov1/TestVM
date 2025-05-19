# Этап 1: Восстановление зависимостей
FROM mcr.microsoft.com/dotnet/sdk:8.0 AS restore
COPY *.csproj .
RUN dotnet restore --runtime linux-x64

# Этап 2: Сборка
FROM mcr.microsoft.com/dotnet/sdk:8.0 AS build
COPY --from=restore /obj ./obj
COPY . .
RUN dotnet build --no-restore -c Release --runtime linux-x64

# Этап 3: Публикация
FROM build AS publish
RUN dotnet publish \
    -c Release \
    -o /app \
    --no-restore \
    --no-build \
    --runtime linux-x64 \
    --self-contained false \
    -p:PublishReadyToRun=true

# Финальный образ
FROM mcr.microsoft.com/dotnet/aspnet:8.0 AS final
WORKDIR /app
COPY --from=publish /app .
ENV ASPNETCORE_URLS=http://+:80
EXPOSE 80
ENTRYPOINT ["dotnet", "Web.dll"]