FROM mcr.microsoft.com/dotnet/aspnet:8.0-azurelinux3.0-distroless-amd64 AS base
USER $APP_UID
WORKDIR /app

FROM mcr.microsoft.com/dotnet/sdk:8.0.402-azurelinux3.0-amd64 AS build
ARG BUILD_CONFIGURATION=Release
WORKDIR /src
COPY ["KRT.GestaoLimites/KRT.GestaoLimites.csproj", "KRT.GestaoLimites/"]
RUN dotnet restore "./KRT.GestaoLimites/KRT.GestaoLimites.csproj"
COPY . .
WORKDIR "/src/KRT.GestaoLimites"
RUN dotnet build "./KRT.GestaoLimites.csproj" -c $BUILD_CONFIGURATION -o /app/build

FROM build AS publish
ARG BUILD_CONFIGURATION=Release
RUN dotnet publish "./KRT.GestaoLimites.csproj" -c $BUILD_CONFIGURATION -o /app/publish /p:UseAppHost=false

FROM base AS final
WORKDIR /app

COPY --from=publish /app/publish .
COPY --from=ghcr.io/ufoscout/docker-compose-wait:latest /wait /wait

EXPOSE 80 443
ENV WAIT_COMMAND="dotnet KRT.GestaoLimites.dll"

USER app
ENTRYPOINT [ "/wait" ]