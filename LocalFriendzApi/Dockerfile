# Etapa base
FROM mcr.microsoft.com/dotnet/aspnet:8.0 AS base
WORKDIR /app
EXPOSE 5005
EXPOSE 5006

# Etapa de constru��o
FROM mcr.microsoft.com/dotnet/sdk:8.0 AS build
WORKDIR /src

# Copiando arquivos .csproj
COPY ["src/LocalFriendzApi/LocalFriendzApi.csproj", "src/LocalFriendzApi/"]
COPY ["src/LocalFriendzApi.Core/LocalFriendzApi.Core.csproj", "src/LocalFriendzApi.Core/"]
COPY ["src/LocalFriendzApi.Application/LocalFriendzApi.Application.csproj", "src/LocalFriendzApi.Application/"]
COPY ["src/LocalFriendzApi.Infrastructure/LocalFriendzApi.Infrastructure.csproj", "src/LocalFriendzApi.Infrastructure/"]
COPY ["src/LocalFriendzApi.Test/LocalFriendzApi.IntegrationTests.csproj", "src/LocalFriendzApi.Test/"]

RUN dotnet tool install --global dotnet-ef
ENV PATH="$PATH:/root/.dotnet/tools"

# Restaura��o de depend�ncias
RUN dotnet restore src/LocalFriendzApi/LocalFriendzApi.csproj


# Copiando todos os arquivos de c�digo-fonte
COPY . .

# Construindo a aplica��o
WORKDIR "/src/src/LocalFriendzApi"
RUN dotnet build "LocalFriendzApi.csproj" -c Release -o /app/build

# Etapa de build dos testes
WORKDIR "/src/src/LocalFriendzApi.Test"
RUN dotnet build "LocalFriendzApi.IntegrationTests.csproj" -c Release -o /app/build

# Etapa de publica��o
FROM build AS publish
WORKDIR "/src/src/LocalFriendzApi"
RUN dotnet publish "LocalFriendzApi.csproj" -c Release -o /app/publish /p:UseAppHost=false

# Etapa final para a aplica��o
FROM base AS final
WORKDIR /app
COPY --from=publish /app/publish .

# Definindo as vari�veis de ambiente para o PostgreSQL
ENV ConnectionStrings__PostgreSqlConnection="Host=db;Database=DB_FIAP_ARQUITETO;Username=postgres;Password=Teste@123"

ENTRYPOINT ["dotnet", "LocalFriendzApi.dll"]
