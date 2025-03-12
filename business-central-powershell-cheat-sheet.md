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

#### Version 14

Import-Module "C:\Program Files\Microsoft Dynamics 365 Business Central\140\Service\NAVAdminTool.ps1" -force

{: .note }
> The NAVAdminTool.ps1 script loads both Microsoft.Dynamics.Nav.Management.dll and Microsoft.Dynamics.Nav.Apps.Management.dll libraries. If NAVAdminTool.ps1 fails to load for some reason, you can always try to load the libraries as follows:
> 
> Import-Module "C:\Program Files\Microsoft Dynamics 365 Business Central\140\Service\Microsoft.Dynamics.Nav.Management.dll" -force
> Import-Module "C:\Program Files\Microsoft Dynamics 365 Business Central\140\Service\Microsoft.Dynamics.Nav.Apps.Management.dll" -force

Import-Module "C:\Program Files (x86)\Microsoft Dynamics 365 Business Central\140\RoleTailored Client\NAVModelTools.ps1" -force

#### Version 15 (up to 22)

Import-Module "C:\Program Files\Microsoft Dynamics 365 Business Central\150\Service\Microsoft.Dynamics.Nav.Management.dll" -force

Import-Module "C:\Program Files\Microsoft Dynamics 365 Business Central\150\Service\Microsoft.Dynamics.Nav.Apps.Management.dll" -force

#### Version 23 onwards

Import-Module "C:\Program Files\Microsoft Dynamics 365 Business Central\\*230*\\Service\Management\Microsoft.Dynamics.Nav.Management.dll" -force

Import-Module "C:\Program Files\Microsoft Dynamics 365 Business Central\\*230*\\Service\Management\Microsoft.Dynamics.Nav.Apps.Management.dll" -force

**Included cmdlets**

Microsoft.Dynamics.Nav.Management.dll
[Microsoft.BusinessCentral.Management Module - Dynamics NAV | Microsoft Learn](https://learn.microsoft.com/en-us/powershell/module/microsoft.dynamics.nav.management/?view=businesscentral-ps-25)

Microsoft.Dynamics.Nav.Apps.Management.dll
[Microsoft.BusinessCentral.Apps.Management Module - Dynamics NAV | Microsoft Learn](https://learn.microsoft.com/en-us/powershell/module/microsoft.dynamics.nav.apps.management/?view=businesscentral-ps-25)



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

## License Management

### Import a license

Imports a license file into a Business Central database

Import-NAVServerLicense <server instance> -LicenseData ([Byte[]]$(Get-Content -Path "<license file>" -Encoding Byte))

```
Import-NAVServerLicense $serverInstance -LicenseData ([Byte[]]$(Get-Content -Path "$licenseFilePath" -Encoding Byte))
```

## Business Central Server Instance Management

### Update configuration / settings

Configures settings for a Business Central Server instance.

Values are written directly to the configuration file for the instance (CustomSettings.config). New setting values do not take effect until you restart the server instance.

Set-NAVServerConfiguration -ServerInstance <bc server instance name> -KeyName <key name> -ServerInstance <server instance>

- Change *serverInstanceName* to the name of the instance on the Microsoft Dynamics BC Server instance.
- Change *keyName* to the configuration key name to be configured. For more info on the available keys check CustomSettings.config file in "C:\Program Files\Microsoft Dynamics 365 Business Central\<version>\Service\CustomSettings.config". 
- Change *keyValue* to the value to be set.
- *(Optional)* Change *applyTo* to the configuration mode to be used:
  - *ConfigFile* or *0* -> *Default*: Saves the change to the configuration file of the server instance. The change will not take effect until the server instance is restarted.
  - *Memory* or *1* -> The setting change is just applied to the server instance's current setting state. This is only applicable for server settings which support dynamic updating. If the specified setting is not dynamically updateable this command will fail.
  - *All* or *2* -> Applies the change to the server instance's current setting state (in memory) and to the configuration file. This is only applicable for server settings that support dynamic updating. If the setting does not support dynamic updating, the cmdlet will fail with an error. The change will not be applied to the current session or the configuration file.


```
Set-NAVServerConfiguration -ServerInstance $serverInstanceName -KeyName $keyName -KeyValue $keyValue -ApplyTo $applyTo
Set-NAVServerConfiguration -ServerInstance bc-w1-22 -KeyName DatabaseServer -KeyValue DatabaseServer.Domain.Com
```



## Web Server Instance Management

### Get web server instances

Get-NAVWebServerInstance

### Add a new web server instance

New-NAVWebServerInstance -WebServerInstance <new web server instance name> -Server <BC Server> -ServerInstance <server instance>

- Change *WebServerInstance* to the name that you want to give the virtual directory for the web server instance. This name will become part of the URL for the Microsoft Dynamics BC Web client application, for example, [http://MyWebServer:8080/WebServerInstance/WebClient](http://mywebserver:8080/WebServerInstance/WebClient). 
- Change *BCServer* to the name of the computer that is running the Microsoft Dynamics BC Server to which you want to connect (ex. localhost)
- Change *ServerInstance* to the name of the instance on the Microsoft Dynamics BC Server.

```
New-NAVWebServerInstance -WebServerInstance $newWebServerInstanceName -Server $BCServer -ServerInstance $serverInstance
New-NAVWebServerInstance -WebServerInstance bc-w1-15 -Server localhost -ServerInstance bc-w1-15
```

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