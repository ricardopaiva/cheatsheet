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

First you need to enter in the SQL command line by typing:

```
sqlcmd
```

### Databases

**List Databases** 

* select name from sys.databases
  

go

**Backup Database**

* BACKUP DATABASE [<database name>] TO DISK = N'<File full path>'

  go
