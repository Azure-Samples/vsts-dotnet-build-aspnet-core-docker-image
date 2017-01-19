FROM microsoft/aspnetcore:1.1.0
WORKDIR /app
COPY netcore/src/netcore/out .
ENTRYPOINT ["dotnet", "netcore.dll"]

### Use the following to run in Docker from Visual Studio ###
# FROM microsoft/aspnetcore:1.1.0
# ENTRYPOINT ["dotnet", "ASPNETCOREAPP.dll"]
# ARG source=.
# WORKDIR /app
# EXPOSE 80
# COPY $source .