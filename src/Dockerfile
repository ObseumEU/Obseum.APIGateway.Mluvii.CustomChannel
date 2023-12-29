#See https://aka.ms/customizecontainer to learn how to customize your debug container and how Visual Studio uses this Dockerfile to build your images for faster debugging.

FROM mcr.microsoft.com/dotnet/aspnet:8.0 AS base
USER app
WORKDIR /app
EXPOSE 8080

FROM mcr.microsoft.com/dotnet/sdk:8.0 AS build
ARG BUILD_CONFIGURATION=Release
WORKDIR /src
COPY ["src/Obseum.Mluvii.CustomChannel/Obseum.Mluvii.CustomChannel.csproj", "Obseum.Mluvii.CustomChannel/"]
COPY ["src/Obseum.Mluvii.CustomChannel.Tests/Obseum.Mluvii.CustomChannel.Tests.csproj", "Obseum.Mluvii.CustomChannel.Tests/"]
RUN dotnet restore "./src/Obseum.Mluvii.CustomChannel/Obseum.Mluvii.CustomChannel.csproj"
COPY . .
WORKDIR "/src/Obseum.Mluvii.CustomChannel"
RUN dotnet build "./Obseum.Mluvii.CustomChannel.csproj" -c $BUILD_CONFIGURATION -o /app/build

FROM build AS test 
RUN dotnet test "Obseum.Mluvii.CustomChannel.Tests/Obseum.Mluvii.CustomChannel.Tests.csproj" --collect:"XPlat Code Coverage" --results-directory:"/app/coverage/";

FROM build AS publish
ARG BUILD_CONFIGURATION=Release
RUN dotnet publish "./Obseum.Mluvii.CustomChannel.csproj" -c $BUILD_CONFIGURATION -o /app/publish /p:UseAppHost=false

FROM base AS final
WORKDIR /app
COPY --from=publish /app/publish .
COPY --from=test /app/coverage ./coverage
ENTRYPOINT ["dotnet", "Obseum.Mluvii.CustomChannel.dll"]