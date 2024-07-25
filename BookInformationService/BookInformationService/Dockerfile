#See https://aka.ms/customizecontainer to learn how to customize your debug container and how Visual Studio uses this Dockerfile to build your images for faster debugging.

FROM mcr.microsoft.com/dotnet/aspnet:8.0 AS base
USER app
WORKDIR /app
EXPOSE 4100
EXPOSE 4101

FROM mcr.microsoft.com/dotnet/sdk:8.0 AS build
ARG BUILD_CONFIGURATION=Release
WORKDIR /src
COPY ["BookInformationService.csproj", "BookInformationService/"]
RUN dotnet restore "./BookInformationService/BookInformationService.csproj"

# Copy the rest of the files (dependencies) and build the project
COPY . ./BookInformationService/ 
WORKDIR "/src/BookInformationService"
RUN dotnet build "./BookInformationService.csproj" -c $BUILD_CONFIGURATION -o /app/build

FROM build AS publish
ARG BUILD_CONFIGURATION=Release
RUN dotnet publish "./BookInformationService.csproj" -c $BUILD_CONFIGURATION -o /app/publish /p:UseAppHost=false
# Copy certificate files to a known location in the image
COPY ./mycert.key /app/publish/
COPY ./mycert.crt /app/publish/


FROM base AS final
WORKDIR /app
COPY --from=publish /app/publish .
ENTRYPOINT ["dotnet", "BookInformationService.dll"]