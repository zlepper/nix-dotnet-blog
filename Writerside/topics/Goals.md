# 0. Goals

The goal of this set of posts is to take our mono-repo at work and build it using Nix.

The repo is a decent sized dotnet solution with several services "Apps", several libraries "Libs", some tests 
and some other bits and bobs. For the purpose of this series I will be writing a very small version of that repository.

This series will start small with just building a single webapi app, and then expand from there. 

## Project structure
To dig a bit more into the project structure we will end up with it looks roughly like this:
```text
/
  repository.sln
  Apps/
    LoginService/
      LoginService.csproj
      Program.cs
    CommentingService/
      CommentingService.csproj
      Program.cs
  Libs/
    Auth/
      Auth.csproj
      Auth.cs
    ServiceBase/
      ServiceBase.csproj
      BaseStartup.cs
    LoginService.Shared/
      LoginService.Shared.csproj
      LoginHappenedEvent.cs
  Tests/
    LoginService.Tests/
      LoginService.Tests.csproj
      LoginServiceTests.cs
```

Each project under `Apps` is a full "microservice" that can run independently. It will probably depend
on some of the libraries under `Libs` and will have some tests under `Tests`.

## Assumptions
I assume you already have both Dotnet and Nix installed on your machine.

I also assume you are familiar with developing dotnet applications. 

Additionally i would strongly recommend going through the [Nix Pills](https://nixos.org/guides/nix-pills/)