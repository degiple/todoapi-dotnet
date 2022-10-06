FROM mcr.microsoft.com/dotnet/aspnet:6.0-focal AS base
WORKDIR /app
EXPOSE 8080

# 1.環境変数の設定
ENV ASPNETCORE_URLS=http://+:8080

# 2.コンテナ実行時のユーザーを設定
# Creates a non-root user with an explicit UID and adds permission to access the /app folder
# For more info, please refer to https://aka.ms/vscode-docker-dotnet-configure-containers
RUN adduser -u 5678 --disabled-password --gecos "" appuser && chown -R appuser /app
USER appuser

# 3.プロジェクトの依存関係や使用ツールを復元し、実行可能なアプリケーションにコンパイルする
FROM mcr.microsoft.com/dotnet/sdk:6.0-focal AS build
WORKDIR /src
COPY ["TodoApi/TodoApi.csproj", "TodoApi/"]
RUN dotnet restore "TodoApi/TodoApi.csproj"
COPY . .
WORKDIR "/src/TodoApi"
RUN dotnet build "TodoApi.csproj" -c Release -o /app/build

# 4.アプリケーションを特定フォルダに発行する
FROM build AS publish
RUN dotnet publish "TodoApi.csproj" -c Release -o /app/publish /p:UseAppHost=false

# 5.発行されたアプリケーションを実行する
FROM base AS final
WORKDIR /app
COPY --from=publish /app/publish .
ENTRYPOINT ["dotnet", "TodoApi.dll"]
