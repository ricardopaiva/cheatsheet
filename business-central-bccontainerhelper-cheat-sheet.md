---
title: BCContainerHelper
nav_order: 4
---

# {{ page.title }}

{: .no_toc }

## Table of contents

{: .no_toc .text-delta }

1. TOC
{:toc}

---

## BCContainerHelper Installation

### Install BCContainerHelper module

```powershell
install-module bccontainerhelper (-force)
```

### Uninstall BCContainerHelper module

```powershell
uninstall-module bccontainerhelper
```

### BCContainerHelper module path

```
C:\Program Files\WindowsPowerShell\Modules\bccontainerhelper\<version>
```

### Get BCContainerHelper installed module version

```
Get-InstalledModule bccontainerhelper
```

### Update BCContainerHelper module

```
Update-Module bccontainerhelper
```

## Dynamics 365 Business Central Installation

### Get Artifacts

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

#### Get BC Artifacts List

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

### Create BC container using artifacts

More info: https://freddysblog.com/2020/06/25/working-with-artifacts/

$containername = 'bcsandbox154qa'
$artifactUrl = Get-BCArtifactUrl -version 15 -country us -select Latest  # D365 BC 2019 Release Wave 2 (V15), Latest update, US
$licenseFilePath = 'C:\DEV\Business Central\Licenses\V15\5259612.flf'

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

### Download and install certificate

After the container creation you get the url to the certificate:

> Files:
> http://bccontainer:8080/ALLanguage.vsix
> http://bccontainer:8080/certificate.cer

To download the container and import to the Trusted Root store do:

```
### Download the certificate from container
$url = 'http://bccontainer:8080/certificate.cer'
$output = 'C:\Temp\certificate.cer'
Invoke-WebRequest -Uri $url -OutFile $output

### Import downloaded certificate to trusted root
Import-Certificate -FilePath $output -CertStoreLocation 'Cert:\LocalMachine\Root' -Verbose
```

### Get NAV/BC Containers List

```
Get-BcContainers
```

### Get NAV/BC Container Server Info

Get-BcContainerServerConfiguration -containerName "<container name>"

```
Get-BcContainerServerConfiguration -containerName "BC140CU4W1"
```

### Get NAV/BC Container Debug Info (info you get after creating the container)

Get-BcContainerDebugInfo -containerName <container name> -ExcludeDockerInfo -ExcludeEnvVars

```
Get-BcContainerDebugInfo -containerName dynamicsbc154 -ExcludeDockerInfo -ExcludeEnvVars
```

### Start / Restart NAV/BC Container

Start-BcContainer -containerName "<container name>"

```
Start-BcContainer -containerName "BC140CU4W1"
Restart-BcContainer -containerName "BC140CU4W1"
Wait-BcContainerReady -containerName "BC140CU4W1"
```

### Stop NAV/BC Container

Stop-BcContainer -containerName "<container name>"

```
Stop-BcContainer -containerName "BC140CU4W1"
```

### Import NAV/BC License

Import a license in the container.

Import-BcContainerLicense -containerName "<container name>" -licenseFile "<license path>" -restart

```
Import-BcContainerLicense -containerName test -licenseFile c:\temp\mylicense.flf -restart
Import-BcContainerLicense -containerName test -licenseFile "https://www.dropbox.com/s/fhwfwjfjwhff/license.flf?dl=1"
```

## Extension Management

### Get installed extension list

Get all installed extensions in a container.

Get-BcContainerAppInfo -containerName "<container name>"

```
Get-BcContainerAppInfo -containerName "test"
```

Get the name of all installed extensions in a container.

(Get-BcContainerAppInfo -containerName "<container name>").Name

```
(Get-BcContainerAppInfo -containerName "test").Name
```

Other parameters:

* -tenant => specifies the tenant from which you want to get the app info;
* -symbolsOnly => specifies whether you only want apps, which are of packagetype SymbolsOnly (Specifying SymbolsOnly ignores the tenant parameter);
* -sort => Specifies how (if any) you want to sort apps based on dependencies to other apps;

### Publish an extension

Publish-BcContainerApp -containerName "<container name>" -appFile "<app file path>"

Optional parameters:

* -sync => synchronized the app;
* -install => installs the app;
* -scope => Global / Tenant;

The below script will **publish**, **sync** and **install** the app in c:\temp\my.app in the NAV container called test. Will install the app in tenant scope as vs code does.

```
Publish-BcContainerApp -containerName "test" -appFile "c:\temp\my.app" -skipVerification -sync -install -scope Tenant
```

If you only publish the app, you can use **Sync-BcContainerApp** and **Install-BcContainerApp** to sync and install the app later.

### Unpublish an extension

Unpublish-BcContainerApp -containerName "<container name>" -appName "<app name>" -tenant <tentant>

Other parameters:

* -uninstall => uninstalls the app before unpublising;
* -publisher => the publisher, used when the name is not enough to identify the app;
* -version => the version, used when there are multiple versions of the app installed;

```
Unpublish-BcContainerApp -containerName "test" -appName "MyApp" -uninstall -tenant default
```

### Clean up the extension before publishing

To clean up the extension, deleting all tables schema, you must sync the app with the -mode flag set to clean. This allows you to installed a previous version of the extension, for example.

Sync-BcContainerApp -containerName "<container name>" -appName "<app name>" -Mode Clean

```
Sync-BcContainerApp -containerName "test" -appName "MyApp" -Mode Clean
```

## Users Management

### Create new user in container

Creates a new user in a container with NavUserPassword credentials

New-BcContainerBcUser -containerName "<container name>" -Credential $credential -PermissionSetId "SUPER"

Other parameters:

* -ChangePasswordAtNextLogOn => forces the user to change the password on logon. Default value: true;

```
$credential = New-Object pscredential 'admin', (ConvertTo-SecureString -String 'P@ssword1' -AsPlainText -Force)
New-BcContainerBcUser -containerName "test" -Credential $credential -PermissionSetId "SUPER"
```

Creates a new user in a container with Windows authentication

New-BcContainerBcUser -containerName "<container name>" -WindowsAccount "<windows account>" -PermissionSetId "SUPER"

```
New-BcContainerBcUser -containerName "test" -WindowsAccount "<domain\username>" -PermissionSetId "SUPER"
```

## Events Logs

**Get the Event log from a NAV/BC Container as an .evtx file**

Get-BcContainerEventLog -containerName <containerName>

```
Get-BcContainerEventLog -containerName "test"
```

## Unit Tests

### Import Test Toolkit to existing container

Import-TestToolkitToBcContainer -containername <container name> -includeTestLibrariesOnly -includePerformanceToolkit

Other parameters:

* -includeTestFrameworkOnly => Only import TestFramework Apps (do not import Test Apps or Test Library apps);
* -testToolkitCountry => Only import TestToolkit objects for a specific country;

```
Import-TestToolkitToBcContainer -containername "test" -includeTestLibrariesOnly -includePerformanceToolkit
```

## Other commands

### Copy file from container to local machine

Copy-FileFromBcContainer -containername <container name> -containerpath <container file path and name to copy> -localpath <local file path and name to copy to>

```
Copy-FileFromBcContainer -containername "test" -containerpath c:/configurationpackages/NAV15.4.W1.ENU.EXTENDED.rapidstart -localpath C:\temp\NAV15.4.W1.ENU.EXTENDED.rapidstart
```

### Import objects to BC

Import-ObjectsToBcContainer -containerName "<container name>" -objectsFile "<objects file path and name>"

### Compile BC objects

Compile-ObjectsInBcContainer -containerName "<container name>" -filter "<filters>"

```
Compile-ObjectsInBcContainer -containerName "BC140CU4W1"
-filter 'Type=Codeunit'
-filter 'Modified=Yes'
-filter 'Compiled=No'
```

### Create a VS Code AL Project Folder based on a Container

Create a VS Code AL Project Folder based on a Container

Create-AlProjectFolderFromBcContainer -container <containerName> -alProjectFolder <AL Project Path> -name <app name> -publisher <app publisher name> -version <app version> -addGIT -useBaseLine

Note: When using the -useBaseLine flag, the -includeAL flag must be set when creating the container.

```
$alProjectFolder = "C:\ProgramData\BcContainerHelper\AL\BaseApp"
Create-AlProjectFolderFromBcContainer -containerName alContainer `
                                       -alProjectFolder $alProjectFolder `
                                       -name "myapp" `
                                       -publisher "Freddy Kristiansen" `
                                       -version "1.0.0.0" `
                                       -AddGIT `
                                       -useBaseLine
```

More info: [Create-AlProjectFolderFromNavContainer](https://github.com/microsoft/navcontainerhelper/blob/5750230e0611d092ca68dbe38a693696ed4e4d92/AppHandling/Create-AlProjectFolderFromNavContainer.ps1)

### Docker Local Shared Folder

C:\ProgramData\BcContainerHelper\Extensions\ <container name>\my

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
