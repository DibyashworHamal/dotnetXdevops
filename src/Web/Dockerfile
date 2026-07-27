FROM mcr.microsoft.com/dotnet/sdk:8.0 AS build
WORKDIR /src

# Copy the Central Package Management file (Required for eShopOnWeb)
COPY ["Directory.Packages.props", "./"]

# Copy ALL project files needed by the Web project
COPY ["src/Web/Web.csproj", "src/Web/"]
COPY ["src/ApplicationCore/ApplicationCore.csproj", "src/ApplicationCore/"]
COPY ["src/Infrastructure/Infrastructure.csproj", "src/Infrastructure/"]
COPY ["src/BlazorAdmin/BlazorAdmin.csproj", "src/BlazorAdmin/"]
COPY ["src/BlazorShared/BlazorShared.csproj", "src/BlazorShared/"]

# Download all dependencies
RUN dotnet restore "src/Web/Web.csproj"

# Now copy the rest of the application code
COPY . .
WORKDIR "/src/src/Web"

# Compile and package the application into a folder called '/app/publish' and disable the generation of an executable host (UseAppHost=false) to reduce the size of the final image.
RUN dotnet publish "Web.csproj" -c Release -o /app/publish /p:UseAppHost=false


FROM mcr.microsoft.com/dotnet/aspnet:8.0 AS final
WORKDIR /app

EXPOSE 8080
USER app

# Copy only the compiled output from the Build Stage (leaving the heavy source code behind)
COPY --from=build /app/publish .

# The command to start the application
ENTRYPOINT ["dotnet", "Web.dll"]