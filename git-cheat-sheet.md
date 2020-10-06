---
title: Git
nav_order: 8
---

# {{ page.title }}
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Git commands

#### View commit history

* List all commits

```console
git log
```

* List only *x* commits
  * git log -p -*x*

```console
git log -p -2
```

* List commits by author
  * git log --author="<author name>"

```
git log --author="Ricardo Paiva Moinhos"
```

* List changes in file
  * git log -- "<filename path>"

```
git log -- "c:\repo\file.cs"
```

* List changes in file (even if it was renamed, deleted, and doesn't exist)

  * git log --follow -- "<filename path>"

```
git log --follow -- "c:\repo\file.cs"
```
