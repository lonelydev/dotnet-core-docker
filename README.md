# How to containerize a .NET Core APP

 - Create and publish a .NET Core app
 - Create and configure a Dockerfile for .NET Core
 - Build a Docker image
 - Create and run a Docker container

 Remember, this is not for ASP.NET Core this is .NET Core. 

 ## Prerequisites:

  - [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download)
  - [Docker Community Edition](https://www.docker.com/products/docker-desktop)
  - a temporary working folder for the Dockerfile and .NET core example. 

 ## Create the .NET Core App

 Go to a local directory and type:

 ```bash 
 dotnet new console -o App -n NetCore.Docker
 ```
Creates a new application folder - `App` and genreates a hello world app. 

Go into the directory and try 

```bash
dotnet run
Hello World!
```

The default template creates an app that prints to the terminal and then immediately terminates. 

Open `Program.cs` in your favourite editor and then paste the following code in it. 

```csharp
using System;
using System.Threading.Tasks;

namespace NetCore.Docker
{
    class Program
    {
        static async Task Main(string[] args)
        {
            var counter = 0;
            var max = args.Length != 0 ? Convert.ToInt32(args[0]) : -1;
            while (max == -1 || counter < max)
            {
                Console.WriteLine($"Counter: {++counter}");
                await Task.Delay(1000);
            }
        }
    }
}
```
Save and close and run `dotnet run` again. 
You'll notice that the app now prints a counter and keeps incrementing the counter until you forcefully stop the application using `Ctrl+C`. 

# Publish the app

Unlike regular dotnet, dotnet core requires you to [publish](https://docs.microsoft.com/en-us/dotnet/core/tools/dotnet-publish) your app. This will create a folder with the application and dependencies code, making it ready for deploying to a target host.

```bash
dotnet publish -c Release
```

The command published the app in Release mode to `.\App\bin\Release\netcoreapp3.1\publish`

# Create Dockerfile
This is the file that docker uses when executing the `docker build` command. This is a text file with no extension. 

Create a file named `Dockerfile` in the application directory, the one containing the `csproj` file. And add the following to it.

```shell
FROM mcr.microsoft.com/dotnet/core/aspnet:3.1
```

The `FROM` keyword requires a fully qualified Docker container image name. 

Microsoft Container Register (mcr) is a syndicate of Docker Hub which hosts publicly accessible containers. The `dotnet/core` segment is the container repository, where as the aspnet segment is the container name. The image is taged with 3.1, which is used for versioning (obviously). For example, the app created in the previous section used the .NET Core 3.1 SDK and the base image referred to in the Dockerfile is tagged with 3.1.

Now from your terminal run:

```bash
docker build -t counter-image -f Dockerfile .
```

Docker will start going through every line in the Dockerfile. The . in the docker build command tells Docker to use the current folder to find the Dockerfile. This command builds the image and creates a local repository named `counter-image` that points to that image. After the command finishes, run `docker images` to view the list of docker images installed. 
