---
services: 
platforms: .Net
author: msonecode
---

# How to build ASP.NET Core application to Docker images in VSTS

## Introduction:

This example demonstrates how to build an ASP.NET Core Application to a Docker image and run the Docker container in VSTS.

## Prerequisites:

***1. Private Linux Agent***

Refer to the **Create and configure a Linux build agent section** in the following blog post:

[https://blogs.msdn.microsoft.com/jcorioland/2016/08/19/build-push-and-run-docker-images-with-visual-studio-team-services/](https://blogs.msdn.microsoft.com/jcorioland/2016/08/19/build-push-and-run-docker-images-with-visual-studio-team-services/)

***2. NET Core***

Install .NET Core on your Linux Agent: [https://www.microsoft.com/net/core#linuxubuntu](https://www.microsoft.com/net/core#linuxubuntu)

***3. npm***

Install npm on your Linux Agent: (Ubuntu) sudo apt-get install -y npm

***4. bower***

Install bower on your Linux Agent: sudo npm install -g bower

***5. Get VSTS ready for Docker***

Refer to the **Get the Docker task** and **Use the Docker extension in VSTS** sections in the following blog post:

[https://blogs.msdn.microsoft.com/jcorioland/2016/08/19/build-push-and-run-docker-images-with-visual-studio-team-services/](https://blogs.msdn.microsoft.com/jcorioland/2016/08/19/build-push-and-run-docker-images-with-visual-studio-team-services/)

## Building and Running the example:


***1. Create an ASP.NET Core application***

If you’re using Visual Studio: [https://docs.microsoft.com/en-us/aspnet/core/tutorials/first-mvc-app/start-mvc](https://docs.microsoft.com/en-us/aspnet/core/tutorials/first-mvc-app/start-mvc)


***2. Check the version of .NETCore framework on Docker Host***

**$ ls -l /usr/share/dotnet/shared/Microsoft.NETCore.App**

e.g. output: drwxr-xr-x 2 root root 12288 Jan  5 04:57 ***1.1.0***

***3. Add Docker Support***

If you’re using Visual Studio: 

[https://marketplace.visualstudio.com/items?itemName=MicrosoftCloudExplorer.VisualStudioToolsforDocker-Preview](https://marketplace.visualstudio.com/items?itemName=MicrosoftCloudExplorer.VisualStudioToolsforDocker-Preview)

***4. Edit/Create Dockerfile***

    FROM microsoft/aspnetcore:1.1.0
    WORKDIR /app
    COPY netcore/src/netcore/out .
    ENTRYPOINT ["dotnet", "netcore.dll"]
This example assumes the project is structured as follows and the application is named “netcore”:

![4](https://raw.githubusercontent.com/shaqian/Docker-VSTS/master/4.png)

***5. (Optional) Update the application from Microsoft.NETCore.App 1.0.1 to 1.1.0***

Right click on your project, click **Manage NuGet Packages**, click **Updates** and update.

***6. (Optional) Edit project.json if the application is updated to Microsoft.NETCore.App 1.1.0***
```shell
{
  "dependencies": {
    ……
    "Microsoft.NETCore.App ": "1.1.0", ==> Delete this line
    ……
  },
  "tools": {
  },
  "frameworks": {
    "netcoreapp1.0": {
      "dependencies": { ==> Add this block if it doesn't exist
        "Microsoft.NETCore.App": {
          "type": "platform",
          "version": "1.1.0"
        }
      }
    }
  }, 

```

***7. Check your code into VSTS***

***8. Create a Build Definition***

***9. (Optional) Add a Shell Script step if private feed is used***

[https://gallery.technet.microsoft.com/How-to-restore-VSTS-feeds-a2b99e9a](https://gallery.technet.microsoft.com/How-to-restore-VSTS-feeds-a2b99e9a)

***10. Add a Command Line step to restore packages***

![10-1](https://raw.githubusercontent.com/shaqian/Docker-VSTS/master/10-1.png)
![10-2](https://raw.githubusercontent.com/shaqian/Docker-VSTS/master/10-2.png)

***11. Add a Command Line step to publish the application***

![11](https://raw.githubusercontent.com/shaqian/Docker-VSTS/master/11.png)

***12. Add a Docker step to build an image***

![12-1](https://raw.githubusercontent.com/shaqian/Docker-VSTS/master/12-1.png)
![12-2](https://raw.githubusercontent.com/shaqian/Docker-VSTS/master/12-2.png)
![12-3](https://raw.githubusercontent.com/shaqian/Docker-VSTS/master/12-3.png)

***13. (Optional) Add a Docker step if you want to push the image to your Docker Registry***

![13](https://raw.githubusercontent.com/shaqian/Docker-VSTS/master/13.png)

***14. Add a Docker step to run the image***

![14](https://raw.githubusercontent.com/shaqian/Docker-VSTS/master/14.png)

***15. Queue a new build***

***16. (Optional) Take a look at the container on Docker Host***

**$ docker ps**
```
CONTAINER ID        IMAGE               COMMAND                CREATED             STATUS              PORTS       NAMES
6f1654ed1667        qisha/netcore:73    "dotnet netcore.dll"   44 seconds ago      Up 42 seconds       0.0.0.0:5000->80/tcp    backstabbing_ramanujan
```

**$ wget -S -O NUL http://localhost:5000**
```
--2017-01-16 01:47:03--  http://localhost:5000/
Resolving localhost (localhost)... 127.0.0.1
Connecting to localhost (localhost)|127.0.0.1|:5000... connected.
HTTP request sent, awaiting response...
  HTTP/1.1 200 OK
...
```

***17. (Optional) Open the port in firewall***

If the host is an Azure VM:

![17](https://raw.githubusercontent.com/shaqian/Docker-VSTS/master/17.png)

