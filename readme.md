# Template for Full-stack Blazor + Web API

Serve a Web API and a Blazor project from the same ASP.NET Core project.

## Try it out

Using bash / zsh:

```
$ dotnet tool install --global dotnet-ef
$ dotnet publish -c Release 
$ cd server
$ dotnet ef database update
$ mkdir bin/Release/net6.0/publish/bin
$ cp bin/database.db* bin/Release/net6.0/publish/bin
$ cd bin/Release/net6.0/publish
$ ./TodoApi
```

