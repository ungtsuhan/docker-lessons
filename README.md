# Docker Lessons

- This example demonstrates how to dockerize an ASP.NET Core application.

## Create .NET Core Web API

- Create .NET Core Web API project with CLI

```
dotnet new webapi -o TrainingWebApi --no-https
```

## Setup Docker File

- View > Command Pallete > `Docker: add` > Add Docker File to workspace
    - .NET: ASP.NET Core
    - Operating System: Window
    - port: 80

- Docker file is generated automatically as below

```
FROM mcr.microsoft.com/dotnet/aspnet:5.0 AS base
WORKDIR /app
EXPOSE 80

ENV ASPNETCORE_URLS=http://+:80

FROM mcr.microsoft.com/dotnet/sdk:5.0 AS build
WORKDIR /src
COPY ["TrainingWebApi.csproj", "./"]
RUN dotnet restore "TrainingWebApi.csproj"
COPY . .
WORKDIR "/src/."
RUN dotnet build "TrainingWebApi.csproj" -c Release -o /app/build

FROM build AS publish
RUN dotnet publish "TrainingWebApi.csproj" -c Release -o /app/publish

FROM base AS final
WORKDIR /app
COPY --from=publish /app/publish .
ENTRYPOINT ["dotnet", "TrainingWebApi.dll"]
```

- `.dockerignore` is also generated

## Build docker image

- Build docker image

```
docker build -t training-webapi:v1 .
```

- See image generated

```
docker images
```

## Run docker image

```
docker run -it --rm -p 8080:80 training-webapi:v1
``` 

## Test Web Api

- call api: `http://localhost:8080/WeatherForecast`