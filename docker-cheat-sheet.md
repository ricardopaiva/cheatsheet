---
title: Docker
nav_order: 2
---

# {{ page.title }}
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Docker commands

### Containers

#### List Containers

* docker container ls
  
  More info: https://docs.docker.com/engine/reference/commandline/container_ls/

* docker ps
  
  Lists all running containers
  
  More info: https://docs.docker.com/engine/reference/commandline/ps/

* docker ps --all
* docker ps -a
  
  Lists all containers

#### Start Containers

*  docker start <container id>

#### Stop Containers

* docker stop <container id>  

#### Remove Containers

  * docker rm <container id>  

#### Rename Containers

  * docker rename <old container id or name>  <new container name>

#### Get container info

* docker inspect <docker id>

#### Get container ip address

* docker inspect -f "{{range .NetworkSettings.Networks}}{{ ".IPAddress" }}{{end}}" <docker id>

#### Copy a local file to the container

* docker cp <local file fullpath> "<container name>:<filename to be created in container>"

  ```
  docker cp "C:\Temp\FileToCopy.txt" "container_foo:FileToCopy.txt"
  ```

### Images

#### Search Image with name

* docker search busybox

#### Restart Container (i.e., if unhealthy)

* docker restart <docker id>

#### List Images

* docker images

#### Remove Images

* docker rmi <image id>

### Networking

#### Create a bridge (nat) network to connect containers to host

(Note that this network must have access to the internet and the dns server when defined otherwise you may have network issues)

* docker network create -d nat <nat name> --subnet <subnet ip> --gateway <gateway ip>

```
docker network create -d nat dockernat --subnet 172.16.1.0/16 --gateway 172.16.1.1
```

#### Create container in created nat network

Use the following parameters in docker run command:

* --network <nat name>

  ```
  --network dockernat
  ```

* --network <nat name> --ip <subnet ip>

  ```
  --network dockernat --ip 172.16.1.2
  ```

#### Connect docker container to a recently created nat network

* docker network connect -ip <container ip> <nat network> <container name>

```
docker network connect --ip 172.16.1.2 dockernat bcsandbox15latest
```

Note: if you're having issues with Docker containers connecting to the host or to the internet, first try to disable the firewall in the host.

Note 2: To get the host ip from the container, use this host.docker.internal (https://docs.docker.com/docker-for-windows/networking/#use-cases-and-workarounds)

Note 3: Configure the firewall with these rules:

* ICMP rule to allow the docker container to ping the host
  * Protocols and ports: ICMPv4
  * Scope: Local IP Addresses = Remote IP Addresses = ip retrieved by the host.docker.internal address
* Rule to allow the docker container to connect to a IIS website in port 49001
  * Protocols and ports:
    * Protocol Type: TCP
    * Local ports = Specific ports = 49001
    * Remote ports = All ports
  * Scope: Local IP Addresses = Remote IP Addresses = ip retrieved by the host.docker.internal address

Note 4: if you're having issues with Docker resolving DNS addresses, take a look at this url: http://www.robertbird.co.uk/development/docker-networking-tips-and-tricks

### Logs

#### Continuous view log output

docker logs --follow <container id>

```
docker logs --follow container_id
```

#### Display only the latest lines

docker logs --tail <nr of lines> <container id>

```
docker logs --tail 100 container_id
```

#### Display logs until a specific point in time

docker logs --follow **--until=<time>** <container id>

```
docker logs --follow --until=10s container_id
```

#### Display logs from a specific point in time

docker logs --follow **--since=<YYYY-MM-DDTHH:MM:SS>** <container id>

```
docker logs --follow --since=2019-03-02 container_id
```

More info: [docker logs | Docker Documentation](https://docs.docker.com/engine/reference/commandline/logs/)

### Events Logs

Note: First create a Powershell session to the container (docker exec -it  <container_id> powershell)

#### Get the latest application logs from the container

Get-Eventlog -newest <top x> <type>

```
Get-Eventlog -newest 20 application
```

#### Get the message for each log

(Get-Eventlog -index <index> <type>).message

```
(Get-Eventlog -index 7004 application).message
```

More info: https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.management/get-eventlog?view=powershell-5.1

Alternative: Check this link: https://www.hougaard.com/get-an-eventlog-from-a-docker-container/

### Other

#### Open new Powershell window in Container

docker exec -it <container id> powershell

```
docker exec -it 8e0967a5b798 powershell
```

#### Connect to SQL from Powershell window in Container

SQL user = sa

SQL password = <same password used in container for the NAVUserPassword>

```
sqlcmd
```

#### Import bacpac file using sqlpackage

sqlpackage.exe /a:Import /sf:<bacpac file path>.bacpac /tsn:<TargetServerName> /tdn:<targetdatabaseName> /tu:<TargerUsername> /tp:<TargetPassword>

```
sqlpackage.exe /a:Import /sf:C:\Temp\NAVBC_V14_CU2_SS.bacpac /tsn:192.168.187.101 /tdn:CRONUS /tu:<username> /tp:<Password>
```

#### Create User Login in Docker SQL, from Powershell window in Container

```
CREATE LOGIN <loginname> WITH PASSWORD = 'WRITE PASSWORD HERE';
GO
CREATE USER <loginname> FOR LOGIN <loginname>;
GO

use master
GO

Changed database context to 'master'.
ALTER ROLE db_owner ADD MEMBER <loginname>
GO

use CRONUS
GO

Changed database context to 'CRONUS'.
ALTER ROLE db_datareader ADD MEMBER <loginname>
GO
```

#### Expose new port from existing container

* Shutdown Docker Engine.

* Find your container path in C:\ProgramData\Docker\containers. Your docker id should match the first characters of one of the folders.

* Edit **config.v2.json** file, adding the new port to **ExposedPorts** section.

**Example**

* **Before**: "ExposedPorts":{"1433/tcp":{},"443/tcp":{},"7045/tcp":{},"7046/tcp":{},"7047/tcp":{},"7048/tcp":{},"7049/tcp":{},"80/tcp":{},"8080/tcp":{}}
* **After**: "ExposedPorts":{"1433/tcp":{},"443/tcp":{},"7045/tcp":{},"7046/tcp":{},"7047/tcp":{},"7048/tcp":{},"7049/tcp":{},"80/tcp":{},"8080/tcp":{},**"4032/tcp":{}**}

**Note:** It's possible to do the same for Port Binding. Port Binding is in **hostconfig.json** file. Syntax: "PortBindings":{"4032/tcp":[{"HostIp":"","HostPort":"4032"}]}

## Troubleshooting

It should be possible to call a rest method from the container to a host url but not if you're publishing from Visual Studio to IISExpress. Publish it and run it in a IIS website instead.

The host address is 'host.docker.internal', which automatically translates to the correct ip.

Invoke-RestMethod 'http://host.docker.internal:49000/api/guid''