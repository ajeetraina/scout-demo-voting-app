# Build image
FROM mcr.microsoft.com/dotnet/sdk:7.0 as build
RUN echo "I am running on amd64, building for amd64"

WORKDIR /source

COPY *.csproj .

RUN dotnet restore \
    && dotnet add package System.Drawing.Common -v 5.0.3

COPY . .

RUN dotnet publish -c release -o /app -r linux-x64 --self-contained false --no-restore --framework net7.0

# App image
FROM mcr.microsoft.com/dotnet/runtime:7.0
WORKDIR /app
COPY --from=build /app .
ENTRYPOINT ["dotnet", "Worker.dll"]

