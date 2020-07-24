---
title: Docker - SQL
nav_order: 5
---

# {{ page.title }}
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## SQL commands

### Databases

**List Databases** 

* Invoke-SqlCmd -Query "select name from sys.databases"

**Backup Database**

* Invoke-SqlCmd -Query "BACKUP DATABASE [<database name>] TO DISK = N'<File full path>'"

