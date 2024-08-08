FROM mcr.microsoft.com/dotnet/aspnet:8.0 AS base
WORKDIR /app
EXPOSE    80

FROM mcr.microsoft.com/dotnet/sdk:8.0 AS build
WORKDIR /src

COPY ["DotNetAPIDockerized/DotNetAPIDockerized.csproj", "DotNetAPIDockerized/"] 
RUN dotnet restore "DotNetAPIDockerized/DotNetAPIDockerized.csproj"

COPY . .
WORKDIR "/src/DotNetAPIDockerized"
RUN dotnet build "DotNetAPIDockerized.csproj" -c Release -o /app/build

FROM build AS publish
RUN dotnet publish "DotNetAPIDockerized.csproj" -c Release -o /app/publish

FROM base AS final
RUN mkdir -p /app/logs/worker
ENV TZ=Asia/Tehran
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone
RUN sed -i 's/DEFAULT@SECLEVEL=2/DEFAULT@SECLEVEL=1/g' /etc/ssl/openssl.cnf
ENV DOTNET_ENVIRONMENT=Production
WORKDIR /app
COPY --from=publish /app/publish .
ENTRYPOINT ["dotnet", "DotNetAPIDockerized.dll"]