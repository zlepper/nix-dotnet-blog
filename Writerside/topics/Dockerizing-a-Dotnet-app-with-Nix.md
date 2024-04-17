# 2. Dockerizing a Dotnet app with Nix

This chapter assumes you have the application built and running from the previous chapter [](Building-a-dotnet-app-with-Nix.md).


First create a `docker.nix` file:
```
{ applicationPkg
, dockerTools
, lib
, buildEnv
}:


dockerTools.buildLayeredImage {
  name = "my-web-api";
  tag = "latest";

  contents = buildEnv {
    name = "image-root";
    paths = [ applicationPkg ];
    pathsToLink = [ "/bin" ];
  };

  config = {
    Cmd = [ "${lib.getExe applicationPkg}" ];
  };
}
```

Then update the `default.nix` file:
```
{ pkgs ? import <nixpkgs> {} }:

rec {
  my-web-api = import ./my-web-api.nix {
        inherit
          (pkgs)
          buildDotnetModule
          dotnetCorePackages
          ;
  };
  my-web-api-docker = import ./docker.nix {
        inherit
          (pkgs)
          lib
          dockerTools
          buildEnv
          ;
        applicationPkg = my-web-api;
 };
}
```

Now we can build the docker image:
```bash
nix-build -A my-web-api-docker
```
{prompt="$"}

And load it into docker:
```bash
docker load < result
```
{prompt="$"}

Then you should be able to run it:
```bash
docker run -it --rm -p 5000:5000 -e ASPNETCORE_URLS=http://0.0.0.0:5000 my-web-api
```
{prompt="$"}

As we haven't specified anything special in the `docker.nix` file we need to pass in the
`ASPNETCORE_URLS` environment variable to tell the application to listen on all interfaces.

Then test it out with curl:
```bash
curl http://localhost:5000/weatherforecast
```
{prompt="$"}


## Conclusion

We have now built and dockerized a dotnet application using Nix. This is a very simple example, but it should be 
enough to get you started with your own applications. However, read on for some more advanced topics.

