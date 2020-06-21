---
title: Docker Cheat Sheet
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

**List Containers** 

* docker container ls
  More info: https://docs.docker.com/engine/reference/commandline/container_ls/

* docker ps
  Lists all running containers
  More info: https://docs.docker.com/engine/reference/commandline/ps/

* docker ps --all
* docker ps -a
  Lists all containers

**Start Containers**

*  docker start <container id>

**Stop Containers**

* docker stop <container id>  

**Remove Containers**

  * docker rm <container id>  

**Rename Containers**

  * docker rename <old container id or name>  <new container name>

**Get container info**

* docker inspect <docker id>

**Get container ip address**

* docker inspect -f "{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}" <docker id>

**Copy a local file to the container**

* docker cp <local file fullpath> "<container name>:<filename to be created in container>"

  ```
  docker cp "C:\Temp\FileToCopy.txt" "container_foo:FileToCopy.txt"
  ```

### Images

**Search Image with name**

* docker search busybox

**Restart Container (i.e., if unhealthy)**

* docker restart <docker id>

**List Images**  

* docker images

**Remove Images**   

* docker rmi <image id>

### Networking

**Create a bridge (nat) network to connect containers to host** (note that this network must have access to the internet and the dns server when defined otherwise you may have network issues)

* docker network create -d nat <nat name> --subnet <subnet ip> --gateway <gateway ip>

```
docker network create -d nat dockernat --subnet 172.16.1.0/16 --gateway 172.16.1.1
```

**Create container in created nat network**

Use the following parameters in docker run command:

* --network <nat name>

  ```
  --network dockernat
  ```

* --network <nat name> --ip <subnet ip>

  ```
  --network dockernat --ip 172.16.1.2
  ```

**Connect docker container to a recently created nat network**

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

#### Copy file from container to local machine

Copy-FileFromNavContainer -containername <container name> -containerpath <container file path and name to copy> -localpath <local file path and name to copy to>

```
Copy-FileFromNavContainer -containername dynamicsbc154 -containerpath c:/configurationpackages/NAV15.4.W1.ENU.EXTENDED.rapidstart -localpath C:\temp\NAV15.4.W1.ENU.EXTENDED.rapidstart
```

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

##### 

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


## Troubleshooting

It should be possible to call a rest method from the container to a host url but not if you're publishing from Visual Studio to IISExpress. Publish it and run it in a IIS website instead.

The host address is 'host.docker.internal', which automatically translates to the correct ip.

Invoke-RestMethod 'http://host.docker.internal:49000//api/guid''