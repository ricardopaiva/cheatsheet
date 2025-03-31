---
title: Update Service
nav_order: 3
---

# {{ page.title }}
Last updated: 2025-03-20

{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Used Variables

$InstanceName = "POS" # or POSMaster, for example



## Modules Management

### Importing BC cmdlets from a Update Service instance

Import-Module (Get-BcModulePath -InstanceName &lt;InstanceName&gt; -Type &lt;Module type&gt;) -Global

* Change *InstanceName* to match the **Update Service instance** (not the Business Central Service instance name)
* *Module type*: **Management**, **Apps**

Imports the Business Central Powershell modules from a Business Central installed using Update Service.

```
Import-Module LsSetupHelper\BusinessCentral\Management

Import-Module (Get-BcModulePath -InstanceName $InstanceName -Type Management) -Global
Import-Module (Get-BcModulePath -InstanceName $InstanceName -Type Apps) -Global
```

From: [go-current-examples/LSCentral/Scripts/Import-BcPsModules.ps1 at master · lsretail/go-current-examples](https://github.com/lsretail/go-current-examples/blob/master/LSCentral/Scripts/Import-BcPsModules.ps1)