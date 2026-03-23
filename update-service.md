---
title: Update Service
nav_order: 3
---

# {{ page.title }}
Last updated: 2026-03-23

{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Used Variables

$InstanceName = "POS" # or "POSMaster", for example


## Modules Management

### Importing BC cmdlets from a Update Service instance

Import-Module (Get-BcModulePath -InstanceName &lt;InstanceName&gt; -Type &lt;Module type&gt;) -Global

* Change *InstanceName* to match the **Update Service instance** (not the Business Central Service instance name)
* *Module type*: **Management**, **Apps**

Imports the Business Central Powershell modules from a Business Central installed using Update Service.

Example:

```
$InstanceName = 'POS'

Import-Module LsSetupHelper\BusinessCentral\Management

Import-Module (Get-BcModulePath -InstanceName $InstanceName -Type Management) -Global
Import-Module (Get-BcModulePath -InstanceName $InstanceName -Type Apps) -Global
```

From: [go-current-examples/LSCentral/Scripts/Import-BcPsModules.ps1 at master · lsretail/go-current-examples](https://github.com/lsretail/go-current-examples/blob/master/LSCentral/Scripts/Import-BcPsModules.ps1)


## Install a local LS Central Instance with Demo Data

Run the following script in an elevated PowerShell console (Run as Administrator):

```powershell
$ErrorActionPreference = 'stop'
Import-Module UpdateService

$InstanceName = 'LSCentral28Dev'    # Name it as you want

$Arguments = @{
  'bc-server' = @{
      AllowSessionCallSuspendWhenWriteTransactionStarted = 'true'
  }
}

$Packages = @(
              # Optional, uncomment to include:
              # @{ Id = 'sql-server-developer-advanced'; VersionQuery = '^-' }
              @{ Id = 'ls-central-demo-database'; VersionQuery = '*^ >=28.0.0-rc' }
              @{ Id = 'bc-web-client'; VersionQuery = '' }
              @{ Id = 'ls-central-app'; VersionQuery = '*^ >=28.0.0-rc' }
)

Write-Host "Installing the following packages:"
$Packages | Get-UscUpdates -InstanceName $InstanceName | Format-Table -AutoSize | Out-String | Write-Host

$Packages | Install-UscPackage -InstanceName $InstanceName -UpdateStrategy 'Manual' -Arguments $Arguments
```

### Install the Update Service Module

If you get an error when importing the Update Service module, run the following script to download and install it:

```powershell
$ErrorActionPreference = 'stop'
$Url = 'https://updateservice.lsretail.com/api/v1/installers/00000000-0000-0000-0000-000000000000/download'
$OutputPath = (Join-Path ([IO.Path]::GetTempPath()) 'UpdateServiceClientInstall.exe')
Write-Host 'Downloading Update Service client...'
Invoke-WebRequest -Uri $Url -OutFile $OutputPath

Write-Host 'Installing Update Service client...'
& $OutputPath --Silent | Out-Null
$env:PSModulePath = [System.Environment]::GetEnvironmentVariable('PSModulePath', 'Machine')
Remove-Item $OutputPath -Force -ErrorAction SilentlyContinue
```

> Close and reopen the PowerShell console after installing for the module to be properly loaded.

