# syntax=docker/dockerfile:1

############################
# 1) Build & publish stage #
############################
FROM mcr.microsoft.com/dotnet/sdk:8.0 AS build
WORKDIR /src

# Copy project files first for better layer caching.
# If your .csproj is not in the repo root, adjust these COPY/WORKDIR lines.
COPY *.csproj ./
RUN dotnet restore

# Copy the rest of the source and publish
COPY . ./
# Publish to /out and force assembly name to "app" so runtime doesn't need to know your project name
RUN dotnet publish -c Release -o /out -p:UseAppHost=false -p:AssemblyName=app

#####################
# 2) Runtime stage  #
#####################
FROM mcr.microsoft.com/dotnet/aspnet:8.0 AS runtime
WORKDIR /app

# ASP.NET listens on 8080 in container by convention
ENV ASPNETCORE_URLS=http://+:8080
EXPOSE 8080

# Copy published output
COPY --from=build /out ./

# Launch the app (generic name thanks to AssemblyName=app)
ENTRYPOINT ["dotnet", "app.dll"]

# Optional: add a simple healthcheck (uncomment if you add curl/wget)
# HEALTHCHECK --interval=30s --timeout=3s --retries=3 CMD curl -f http://localhost:8080/health || exit 1

