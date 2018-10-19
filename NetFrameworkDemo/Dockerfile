# escape=`

#Depending on the operating system of the host machines(s) that will build or run the containers, the image specified in the FROM statement may need to be changed.
#For more information, please see https://aka.ms/containercompat


FROM microsoft/iis as base

SHELL ["powershell", "-Command", "$ErrorActionPreference = 'Stop';"]

#
# Download and install IIS Administration API
RUN `
  New-Item -Type Directory C:\Setup | Out-Null ; `
  Invoke-WebRequest 'https://download.visualstudio.microsoft.com/download/pr/7df005cf-404c-4ab8-b25b-ce692a78fbf0/14a40838837da976a9337c0f9e604b30/dotnet-hosting-2.2.0-preview3-35497-win.exe' -UserAgent '' -OutFile C:/setup/dotnet-hosting-2.2.0-preview3-35497-win.exe ; `
  $process = start-process -Filepath C:/setup/dotnet-hosting-2.2.0-preview3-35497-win.exe -ArgumentList  @('/install', '/q', '/norestart', 'OPT_NO_RUNTIME=1', 'OPT_NO_SHAREDFX=1') -Wait -PassThru ; `
  `
  if ($process.ExitCode -ne 0) { `
    exit $process.ExitCode ; `
  } `
  Remove-Item -Force C:/setup/dotnet-hosting-2.2.0-preview3-35497-win.exe


FROM microsoft/dotnet-framework:4.7.2-sdk AS build
WORKDIR /src
COPY ["NetFrameworkDemo.csproj", "NetFrameworkDemo/"]
RUN dotnet restore "NetFrameworkDemo/NetFrameworkDemo.csproj"
WORKDIR "/src/NetFrameworkDemo"
COPY . .
RUN dotnet build "NetFrameworkDemo.csproj" -c Release -o \app

FROM build AS publish
RUN dotnet publish "NetFrameworkDemo.csproj" -c Release -o \publish

FROM base AS final
WORKDIR /inetpub/wwwroot
COPY --from=publish \publish .
ENTRYPOINT ["NetFrameworkDemo.exe"]