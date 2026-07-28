FROM mcr.microsoft.com/dotnet/sdk:8.0 AS build
WORKDIR /src

# 1. Copy the Central Package Management file
COPY ["Directory.Packages.props", "./"]

# 2. Copy the API project and its required dependencies
COPY ["src/PublicApi/PublicApi.csproj", "src/PublicApi/"]
COPY ["src/ApplicationCore/ApplicationCore.csproj", "src/ApplicationCore/"]
COPY ["src/Infrastructure/Infrastructure.csproj", "src/Infrastructure/"]
COPY ["src/BlazorShared/BlazorShared.csproj", "src/BlazorShared/"]

# 3. Restore dependencies
RUN dotnet restore "src/PublicApi/PublicApi.csproj"

# 4. Copy the rest of the source code
COPY . .
WORKDIR "/src/src/PublicApi"

# 5. Compile the API
RUN dotnet publish "PublicApi.csproj" -c Release -o /app/publish /p:UseAppHost=false

FROM mcr.microsoft.com/dotnet/aspnet:8.0 AS final
WORKDIR /app

EXPOSE 8080
USER app

COPY --from=build /app/publish .

ENTRYPOINT ["dotnet", "PublicApi.dll"]