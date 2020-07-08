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

### Create BC container using artifacts

More info: https://freddysblog.com/2020/06/25/working-with-artifacts/

#### Obtaining BC artifacts

$artifactUrl = Get-BCArtifactUrl -version <version filter> -country <country code> -select <> -type <onprem or sandbox> 



- **storageAccount** (bcinsider or **bcartifacts**). The storage account determines where to get the artifacts. bcartifacts is the storage account, which contains all public artifacts with Business Central and NAV.
- **type** (onprem or **sandbox** - default is blank = sandbox) determines whether to get onprem artifacts (shipped builds) or sandbox artifacts (Saas builds). If you are looking for a build, which matches your saas version, you need to use sandbox.
- **version** (default is blank = all versions) is the version you are filtering for. You can specify a full version number or a partial version number. The function will try to match your request.
- **country** (default is blank = all countries) is the localization you are filtering for. Onprem countries follows onprem releases (na for north america), sandbox countries follows saas countries (us, ca and mx).*Note that in 0.7.0.7 there is a bug, which returns the platform as a country. The platform cannot be used as a country:-(*
- select (all, closest, secondToLastMajor or latest) determines which artifact urls you get.
  - **latest** (which is the default) will return the latest image in the list
  - **all** will return an array of all artifact urls matching the filter
  - **closest** requires you to supply a full version number and the function will return the closest version (where Major and Minor version MUST match).
  - **secondToLastMajor** is used in CI/CD scenarios to identify next minor release.
- **sasToken** is the shared access signature token to use when requesting artifacts from a secured storage account (like bcinsider).




```
$artifactUrl = Get-BCArtifactUrl -version 15.2 -country us  # D365 BC 2019 Release Wave 2 (V15), Update 15.2, US
$artifactUrl = Get-BCArtifactUrl -version 15 -country us -select Latest  # D365 BC 2019 Release Wave 2 (V15), Latest update, US
$artifactUrl = Get-BCArtifactUrl -version 16 -country us -select Latest  # D365 BC 2020 Release Wave 1 (V16), Latest update, US
```

#### Creating BC container using artifacts

$containername = 'bcsandbox154qa'
$artifactUrl = Get-BCArtifactUrl -version 15 -country us -select Latest  # D365 BC 2019 Release Wave 2 (V15), Latest update, US
$licenseFilePath = 'C:\enChoice\Business Central\Licenses\V15\5259612.flf'

New-BcContainer -accept_eula -containerName <container name> -artifactUrl $artifactUrl -useSSL -updateHosts -auth <Authentication Mode> -Verbose -additionalParameters @("--network nat", "--ip 172.28.80.5") -licenseFile <license path> 

* -includeAL -> To include the base app
* -includeTestLibrariesOnly -> To include test libraries
* -includeTestToolkit -> To include test libraries and all the standard tests.

```powershell
$containername = 'bcsandbox15'
$artifactUrl = Get-BCArtifactUrl -version 15 -country us -select Latest  # D365 BC 2019 Release Wave 2 (V15), Latest update, US
$licenseFilePath = 'C:\License Path\license.flf'
$credential = New-Object pscredential 'admin', (ConvertTo-SecureString -String 'P@ssword1' -AsPlainText -Force)

New-BcContainer `
    -accept_eula `
    -containerName $containername `
    -artifactUrl $artifactUrl `
    -Credential $credential `
    -useSSL `
    -updateHosts `
    -auth NavUserPassword `
    -Verbose `
    -licenseFile $licenseFilePath

```

### Get BC Artifacts List

```
Write-Host -ForegroundColor Yellow "Get US sandbox artifact url for current version (Latest)"
Get-BCArtifactUrl -country "us"

Write-Host -ForegroundColor Yellow "Get all US sandbox artifact urls"
Get-BCArtifactUrl -country "us" -select All

Write-Host -ForegroundColor Yellow "Get US sandbox artifact url for a version closest to 16.2.13509.13700"
Get-BCArtifactUrl -country "us" -version "16.2.13509.13700" -select Closest

Write-Host -ForegroundColor Yellow "Get latest 16.1 US sandbox artifact url"
Get-BCArtifactUrl -country "us" -version "16.1"

Write-Host -ForegroundColor Yellow "Get latest 15.x US sandbox artifact url"
Get-BCArtifactUrl -country "us" -version "15"

Write-Host -ForegroundColor Yellow "Get all Danish NAV and Business Central artifact urls"
Get-BCArtifactUrl -type OnPrem -country "dk" -select All
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

## Extension Management

### Publish an extension

Publish-NavContainerApp -containerName "<container name>" -appFile "<app file path>"

Optional parameters:

* -sync => synchronized the app;
* -install => installs the app;

The below script will **publish**, **sync** and **install** the app in c:\temp\my.app in the NAV container called test.

```
Publish-NavContainerApp -containerName "test" -appFile "c:\temp\my.app" -skipVerification -sync -install
```

If you only publish the app, you can use **Sync-NavContainerApp** and **Install-NavContainerApp** to sync and install the app later.



## Other commands

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

## Dynamics 365 Business Central Installation (Deprecated methods)

### Create BC container (will download image if necessary)

* -includeAL
* To include the base app

```powershell
New-BCContainer -accept_eula -containerName 'BC140CU4W1' `
				-imageName 'mcr.microsoft.com/businesscentral/onprem:14.5.35970.0-w1-ltsc2019' `
                -updateHosts
```
