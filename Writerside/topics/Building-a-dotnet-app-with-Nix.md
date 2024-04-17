# 1. Building a dotnet app with Nix

In this first part we will take the default webapi starter project and build it using Nix.

## Creating a project
To create the webapi project simply do:
```bash
dotnet new webapi --name my-web-api
cd my-web-api
```
{prompt="$"}

And just to confirm that it works before we start messing with Nix:
```bash
dotnet run
```
{prompt="$"}

You should see the application starting and the port being written to the console. Test it using curl:
```bash
curl http://localhost:5120/weatherforecast
```
{prompt="$"}

If you get some json output you are good to go and can stop the application with `Ctrl+C`.

Go back to the root of the project:
```bash
cd ..
```
{prompt="$"}

## Building using Nix
First we add a .nix file for building the application itself:
`my-web-api.nix`
```
{ buildDotnetModule, dotnetCorePackages }:

buildDotnetModule rec {
  pname = "my-web-api";
  version = "0.1";

  src=./.;
  projectFile = "./my-web-api/my-web-api.csproj";
  nugetDeps = ./deps.nix;

  dotnet-sdk = dotnetCorePackages.sdk_8_0;
  dotnet-runtime = dotnetCorePackages.aspnetcore_8_0;

  meta = {
     mainProgram = "my-web-api";
  };

  executables = [meta.mainProgram];
}
```

Next add the `default.nix` file:
```
{ pkgs ? import <nixpkgs> {}}:

{
  my-web-api = import ./my-web-api.nix { 
    inherit 
      (pkgs)
      buildDotnetModule 
      dotnetCorePackages
      ;
  };
}
```

Now we need to generate the `deps.nix` file:
```bash
nix-build -A my-web-api.fetch-deps
./result
```

And finally we can build the application:
```bash
nix-build -A my-web-api
```

And run it to see it working:
```bash
./result/bin/my-web-api
```

Curl it to double-check:
```bash
curl http://localhost:5000/weatherforecast
```

## Conclusion
We have now taken a very simple dotnet core webapi application and build and ran it using Nix.

In the next chapter we will be taking a look at putting it into a docker container.

If you run the `tree` command you should see something like this:
```text
.
├── default.nix
├── deps.nix
├── my-web-api
│   ├── appsettings.Development.json
│   ├── appsettings.json
│   ├── my-web-api.csproj
│   ├── my-web-api.http
│   ├── Program.cs
│   └── Properties
│       └── launchSettings.json
└── my-web-api.nix
```