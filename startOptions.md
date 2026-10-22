# Inicialização do Jellyfin Server

## Pré-requisitos

- .NET 10 SDK (`global.json` exige 10.0.x).
- FFmpeg (neste ambiente: `C:\temp\bin\ffmpeg.exe`).
- Para hospedar a interface web: arquivos do [jellyfin-web](https://github.com/jellyfin/jellyfin-web) (pasta `dist`) ou de uma instalação existente do Jellyfin.

## Sequência de execução (PowerShell)

```powershell
cd D:\Development\jellyfin_server

# 1. Adicionar o FFmpeg ao PATH da sessão atual (vale só para esta janela)
$env:Path += ";C:\temp\bin"

# 2. Verificar FFmpeg e SDK
ffmpeg -version
dotnet --list-sdks

# 3. Restaurar dependências
dotnet restore .\Jellyfin.sln

# 4. Compilar o servidor em Release (ver "Erro CS9057" abaixo)
dotnet build .\Jellyfin.Server\Jellyfin.Server.csproj -c Release

# 5a. Executar COM interface web (requer o build do jellyfin-web, ver seção abaixo)
dotnet run -c Release --no-build --project .\Jellyfin.Server\Jellyfin.Server.csproj -- --webdir "D:\Development\jellyfin-web\dist" --ffmpeg "C:\temp\bin\ffmpeg.exe"

# 5b. OU executar SEM interface web (apenas API)
dotnet run -c Release --no-build --project .\Jellyfin.Server\Jellyfin.Server.csproj -- --nowebclient --ffmpeg "C:\temp\bin\ffmpeg.exe"
```

## Build do cliente web (jellyfin-web)

O erro "content directory is either invalid or empty" ocorre quando a pasta de `--webdir` não tem o build
do cliente web (a pasta `D:\Development\jellyfin_server\jellyfin-web\dist` estava vazia). Requer Node.js e Git.
O clone em `D:\Development\jellyfin-web` (versão 13.0.0, igual à do servidor) é compilado assim:

```powershell
cd D:\Development\jellyfin-web
npm ci
npm run build:production   # gera dist\index.html (~3 min)
```

Se ainda não existir, clone antes com `git clone https://github.com/jellyfin/jellyfin-web.git`.

## Erro CS9057 (analyzer `Jellyfin.CodeAnalysis.dll` requer compilador 5.9.0.0)

O build em Debug carrega o analisador do projeto, compilado contra o Roslyn 5.9, mas o SDK instalado
(10.0.103) traz o Roslyn 5.0. Soluções:

- Compilar em **Release** (o analisador só é usado em Debug), como nos comandos acima; ou
- Instalar um .NET 10 SDK mais recente (https://dotnet.microsoft.com/download/dotnet/10.0) para usar Debug.

## Acessos

- Interface web: http://localhost:8096
- API (Swagger): http://localhost:8096/api-docs/swagger/index.html

## FFmpeg no PATH

- Sessão atual: `$env:Path += ";C:\temp\bin"`
- Permanente (usuário; abra um novo terminal depois):

```powershell
[Environment]::SetEnvironmentVariable("Path", [Environment]::GetEnvironmentVariable("Path","User") + ";C:\temp\bin", "User")
```

- Alternativa sem alterar o PATH: usar `--ffmpeg "C:\temp\bin\ffmpeg.exe"` (já incluído acima).

## Observações

- Para encerrar o servidor, pressione `Ctrl+C`.
- Apenas `--webdir` ou `--nowebclient` deve ser usado em cada execução.
