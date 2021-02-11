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

* List all commits (in the active branch)

```console
git log
```

* List all commits (in all branches)

```console
git log --all
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

* List changes in file (in the active branch)
  * git log -- "<filename path>"

```
git log -- "c:\repo\file.cs"
```

* List changes in file (in all branches)
  * git log --all -- "<filename path>"

```
git log -all -- "c:\repo\file.cs"
```

* List changes in any file in a folder (in all branches)
  * git log --all -- "<folder path>"

```
git log -all -- "c:\repo\"
```

* List changes in file (even if it was renamed, deleted, and doesn't exist)
* git log --follow -- "<filename path>"

```
git log --follow -- "c:\repo\file.cs"
```
