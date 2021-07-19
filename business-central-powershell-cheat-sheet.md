---
title: Business Central Powershell
nav_order: 3
---

# {{ page.title }}
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Modules Management

### Import Modules

Import-Module "C:\Program Files\Microsoft Dynamics 365 Business Central\140\Service\NAVAdminTool.ps1" -force

Import-Module "C:\Program Files (x86)\Microsoft Dynamics 365 Business Central\140\RoleTailored Client\NAVModelTools.ps1" -force

## Used Variables

$serverInstance = "PT_BC_CU2"

$extensionName = "Extension1"

$extensionVersion = "1.0.0.0"


## Extensions Management

### Get all extensions list

Get-NAVAppInfo -ServerInstance <server instance> -Tenant <tenant name>

Skip the tenant parameter to see all tenant extensions.

```
Get-NAVAppInfo -ServerInstance $serverInstance
```

### Publish extension

Publish-NAVApp -ServerInstance <server instance> -Path <path>

```
Publish-NAVApp -ServerInstance $serverInstance -Path 'C:\Temp\navapp.app'
```

### Install extension

Install-NAVApp -ServerInstance <server instance> -Name <extension name> -Version <extension version>

```
Install-NAVApp -ServerInstance $serverInstance -Name $extensionName -Version $extensionVersion
```

### Uninstall extension

Uninstall-NAVApp -ServerInstance <server instance> -Name <extension name> -Version <extension version>

```
Uninstall-NAVApp -ServerInstance $serverInstance -Name $extensionName -Version $extensionVersion
```

### Uninstall all extensions at once

```
Get-NAVAppInfo -ServerInstance $serverInstance -Tenant default | % { Uninstall-NAVApp -ServerInstance $serverInstance -Name $_.Name -Version $_.Version } 
```

### Unpublish extensions

Unpublish-NAVApp -ServerInstance <server instance> -Name <extension name> -Version <extension version>

```
Unpublish-NAVApp -ServerInstance $serverInstance -Name $extensionName -Version $extensionVersion 
```

## Users Management

### Add a new Windows Account user 

New-NavServerUser -WindowsAccount <windows account> -ServerInstance <server instance>
New-NavServerUserPermissionSet -WindowsAccount <windows account> -ServerInstance <server instance> -PermissionSetId <permission set id>

```
New-NavServerUser -WindowsAccount 'domain.local\ricardo' -ServerInstance $serverInstance
New-NavServerUserPermissionSet -WindowsAccount 'domain.local\ricardo' -ServerInstance $serverInstance -PermissionSetId SUPER
```

## Web Server Instance Management

### Add a new web server instance 

New-NAVWebServerInstance -WebServerInstance <new web server instance name> -Server <BC Server> -ServerInstance <server instance>

- Change *WebServerInstance* to the name that you want to give the virtual directory for the web server instance. This name will become part of the URL for the Microsoft Dynamics BC Web client application, for example, [http://MyWebServer:8080/WebServerInstance/WebClient](http://mywebserver:8080/WebServerInstance/WebClient). 
- Change *BCServer* to the name of the computer that is running the Microsoft Dynamics BC Server to which you want to connect.
- Change *ServerInstance* to the name of the instance on the Microsoft Dynamics BC Server.

```
New-NAVWebServerInstance -WebServerInstance $newWebServerInstanceName -Server $BCServer -ServerInstance $serverInstance
```

## 

## Objects Exporting

### Export all objects from NAV Database to text file ####

Export-NAVApplicationObject <database> "<Export to file path>" -DatabaseServer <SQL Server  Instance> -ExportTxtSkipUnlicensed

**Example**: Exports all objects from NAV2015AP-APP-TEST to NAV2015-All-Objects.txt

``` 
Export-NAVApplicationObject NAV2015AP-APP-TEST “E:\NAV2015-Objects\All-Objs\NAV2015-All-Objects.txt" -DatabaseServer MYP-RPAIVA\SQL2017 -ExportTxtSkipUnlicensed
```

### Export filtered objects ####

Export-NAVApplicationObject <database> "<Export to file path>" -Filter '<filter>' -DatabaseServer <SQL Server  Instance> -ExportTxtSkipUnlicensed

**Example**: Exports codeunits 80..84 from the *NAV2015AP-APP-TEST* to COD80-84.txt

``` 
Export-NAVApplicationObject NAV2015AP-APP-TEST “E:\NAV2015-Objects\All-Objs\NAV2015-All-Objects.txt" -Filter 'Type=Codeunit;Id=80..84' -DatabaseServer MYP-RPAIVA\SQL2017 -ExportTxtSkipUnlicensed
```

###  Exports all objects and split into single-object files

Export-NAVApplicationObject "<Export to file path>" -Filter '<filter>' -DatabaseServer <SQL Server  Instance> -DatabaseName <database> -ExportTxtSkipUnlicensed | Split-NAVApplicationObjectFile -Destination "<Split files to path>"

**Example**: Exports all objects and split V2015AP-APP-TEST* to Splitted-Objs folder

``` 
Export-NAVApplicationObject "E:\NAV2015-Objects\All-Objs\NAV2015-All-Objects.txt" -DatabaseServer MYP-RPAIVA\SQL2017 -DatabaseName NAV2015AP-APP-TEST -ExportTxtSkipUnlicensed | Split-NAVApplicationObjectFile -Destination "E:\NAV2015-Objects\Splitted-Objs"
```