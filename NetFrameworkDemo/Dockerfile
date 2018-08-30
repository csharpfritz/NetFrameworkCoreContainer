#Depending on the operating system of the host machines(s) that will build or run the containers, the image specified in the FROM statement may need to be changed.
#For more information, please see https://aka.ms/containercompat

FROM microsoft/dotnet-framework:4.7.2-runtime as base
ENV ASPNETCORE_URLS "http://*:80"
EXPOSE 80

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
WORKDIR /app
COPY --from=publish \publish .
ENTRYPOINT ["NetFrameworkDemo.exe"]