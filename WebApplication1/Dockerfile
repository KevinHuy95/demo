# ----------------------------------------------------
# 1. Base Runtime Stage (Dùng để chạy trên Production / K8s)
# ----------------------------------------------------
FROM mcr.microsoft.com/dotnet/aspnet:9.0 AS base
USER $APP_UID
WORKDIR /app
EXPOSE 8080
EXPOSE 8081

# ----------------------------------------------------
# 2. Build Stage (Dùng .NET SDK để biên dịch)
# ----------------------------------------------------
FROM mcr.microsoft.com/dotnet/sdk:9.0 AS build
ARG BUILD_CONFIGURATION=Release
WORKDIR /src

# Copy đúng file csproj vào thư mục con và restore thư viện NuGet
COPY ["WebApplication1/WebApplication1.csproj", "WebApplication1/"]
RUN dotnet restore "WebApplication1/WebApplication1.csproj"

# Copy toàn bộ mã nguồn của repository vào /src
COPY . .

# Chuyển vào thư mục chứa project để build
WORKDIR "/src/WebApplication1"
RUN dotnet build "WebApplication1.csproj" -c $BUILD_CONFIGURATION -o /app/build

# ----------------------------------------------------
# 3. Publish Stage (Biên dịch ra file nhị phân thành phẩm)
# ----------------------------------------------------
FROM build AS publish
ARG BUILD_CONFIGURATION=Release
RUN dotnet publish "WebApplication1.csproj" -c $BUILD_CONFIGURATION -o /app/publish /p:UseAppHost=false

# ----------------------------------------------------
# 4. Final Stage (Image tối giản đẩy lên Docker Hub)
# ----------------------------------------------------
FROM base AS final
WORKDIR /app
COPY --from=publish /app/publish .
ENTRYPOINT ["dotnet", "WebApplication1.dll"]
