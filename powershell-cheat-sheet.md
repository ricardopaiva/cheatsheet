---
title: Powershell
nav_order: 9
---

# {{ page.title }}

{: .no_toc }

## Table of contents

{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Module Management

### Install module

Downloads one or more modules from a repository, and installs them on the local computer.

```powershell
Install-Module <module name> (-force)
```

More info and examples: [Install-Module (PowerShellGet) - PowerShell | Microsoft Docs](https://docs.microsoft.com/en-us/powershell/module/powershellget/install-module?view=powershell-7.2)

### Uninstall module

Uninstalls a module.

```powershell
Uninstall-Module <module name>
```

More info and examples: [Uninstall-Module (PowerShellGet) - PowerShell | Microsoft Docs](https://docs.microsoft.com/en-us/powershell/module/powershellget/uninstall-module?view=powershell-7.2)

### List all installed modules on the computer

List the modules that can be imported from the PSModulePath.

```powershell
Get-Module -ListAvailable
```

Search for an installed module with a specific name

```powershell
Get-Module -ListAvailable | Where-Object {$_.Name -like '*Exchange*'}
```

### List modules imported in the current session

List the modules imported in the current session 

```powershell
Get-Module
```

More info and examples: [Get-Module (Microsoft.PowerShell.Core) - PowerShell | Microsoft Docs](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/get-module?view=powershell-7.2)

### Load a module

Adds modules to the current session.

```powershell
Install-Module -Name <module name>
```

More info and examples: [Import-Module (Microsoft.PowerShell.Core) - PowerShell | Microsoft Docs](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/import-module?view=powershell-7.2)
