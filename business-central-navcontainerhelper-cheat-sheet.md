---
title: NAVContainerHelper
nav_order: 4
---

# {{ page.title }}

{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## NAVContainerHelper Installation

### Install NAVContainerHelper module

```powershell
install-module navcontainerhelper (-force)
```

### Uninstall NAVContainerHelper module

```powershell
uninstall-module navcontainerhelper
```

### NAVContainerHelper module path

```
C:\Program Files\WindowsPowerShell\Modules\navcontainerhelper\<version>
```

### Get NAVContainerHelper installed module version

```
Get-InstalledModule navcontainerhelper
```

### Update NAVContainerHelper module

```
Update-Module navcontainerhelper
```

## Dynamics 365 Business Central Installation

### Create BC container (will download image if necessary)

* -includeAL
* To include the base app

```powershell
New-BCContainer -accept_eula -containerName 'BC140CU4W1' `
				-imageName 'mcr.microsoft.com/businesscentral/onprem:14.5.35970.0-w1-ltsc2019' `
                -updateHosts
```

### Get NAV/BC Containers List

```
Get-NavContainers
```

### Get NAV/BC Container Server Info

Get-NavContainerServerConfiguration -containerName "<container name>"

```
Get-NavContainerServerConfiguration -containerName "BC140CU4W1"
```

### Get NAV/BC Container Debug Info (info you get after creating the container)

Get-NavContainerDebugInfo -containerName <container name> -ExcludeDockerInfo -ExcludeEnvVars

```
Get-NavContainerDebugInfo -containerName dynamicsbc154 -ExcludeDockerInfo -ExcludeEnvVars
```

### Start / Restart NAV/BC Container

Start-NavContainer -containerName "<container name>"

```
Start-NavContainer -containerName "BC140CU4W1"
Restart-NavContainer -containerName "BC140CU4W1"
Wait-NavContainerReady -containerName "BC140CU4W1"
```

### Stop NAV/BC Container

Stop-NavContainer -containerName "<container name>"

```
Stop-NavContainer -containerName "BC140CU4W1"
```

### Import objects to BC

Import-ObjectsToNavContainer -containerName "<container name>" -objectsFile "<objects file path and name>"

### Compile BC objects

Compile-ObjectsInNavContainer -containerName "<container name>" -filter "<filters>"

```
Compile-ObjectsInNavContainer -containerName "BC140CU4W1"
-filter 'Type=Codeunit'
-filter 'Modified=Yes'
-filter 'Compiled=No'
```

### Create a VS Code AL Project Folder based on a Container

Create a VS Code AL Project Folder based on a Container

Create-AlProjectFolderFromNavContainer -container <containerName> -alProjectFolder <AL Project Path> -name <app name> -publisher <app publisher name> -version <app version> -addGIT -useBaseLine

Note: When using the -useBaseLine flag, the -includeAL flag must be set when creating the container.

```
$alProjectFolder = "C:\ProgramData\NavContainerHelper\AL\BaseApp"
Create-AlProjectFolderFromNavContainer -containerName alContainer `
                                       -alProjectFolder $alProjectFolder `
                                       -name "myapp" `
                                       -publisher "Freddy Kristiansen" `
                                       -version "1.0.0.0" `
                                       -AddGIT `
                                       -useBaseLine
```

More info: [Create-AlProjectFolderFromNavContainer](https://github.com/microsoft/navcontainerhelper/blob/5750230e0611d092ca68dbe38a693696ed4e4d92/AppHandling/Create-AlProjectFolderFromNavContainer.ps1)

### Docker Local Shared Folder

C:\ProgramData\NavContainerHelper\Extensions\ <container name>\my

Folder inside container: C:\run\my
