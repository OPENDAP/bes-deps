
This repo uses ```git LFS``` to store the binary tar balls that hold
the source code for the BES dependencies. Here's some background
on using ```git LFS```.

## One-time installation
Install the LFS software.
### macOS
```brew install git-lfs```

### Ubuntu / Debian
```sudo apt-get update && sudo apt-get install git-lfs```

### Fedora
```sudo dnf install git-lfs```

### Windows (PowerShell as Admin)
```choco install git-lfs```

The activate the software.
```git lfs install```

## It should just work
Using ```git clone https://github.com/OPENDAP/bes-deps``` should download
all the tar balls in their entirety. There is a way to force the ```git clone```
to _not_ download the actual tar balls but instead only get the hashes that
reference them in the GitHub LFS store, but by default ```git clone``` should
just work.

## How to see the hashes
Here are some useful ```git lfs``` commands.

See the files in LFS: ```git lfs ls-files```
```bash
3fffa08146 - downloads/HDF-EOS2.19v1.00.tar.Z
0db2b42c16 - downloads/STARE-1.1.0.tar.bz2
162ea71d21 - downloads/bison-3.3.tar.xz
5b65a20d5c - downloads/cfitsio-3.49.tar.gz
0fa36ee34d - downloads/gdal-3.11.4.tar.gz
b9a09321b9 - downloads/gridfields-1.0.5.tar.gz
834605e897 - downloads/hdf4-hdf4.3.0.e.tar.gz
56bbf9577a - downloads/hdf5-1.10.10.tar.bz2
33426d493f - downloads/jpegsrc.v6b.tar.gz
3ed83ab851 - downloads/netcdf-490-e.tar.gz
368fe04682 - downloads/openjpeg-2.5.3.tar.gz
65705ecd98 - downloads/proj-9.7.0.tar.gz
bf6db7fae3 - downloads/sqlite-autoconf-3340000.tar.gz
```

To clone the repo but not actually download the big stuff: 
```GIT_LFS_SKIP_SMUDGE=1 git clone https://github.com/OPENDAP/bes-deps```

Here's what would be in the ```downloads``` directory:
```bash
idoru-2:bes-deps-test jimg$ ls -l downloads/
total 19992
-rw-r--r--  1 jimg  staff       132 Sep 17 14:33 HDF-EOS2.19v1.00.tar.Z
-rw-r--r--  1 jimg  staff       132 Sep 17 14:33 STARE-1.1.0.tar.bz2
-rw-r--r--  1 jimg  staff       132 Sep 17 14:33 bison-3.3.tar.xz
-rw-r--r--  1 jimg  staff       132 Sep 17 14:33 cfitsio-3.49.tar.gz
-rw-r--r--  1 jimg  staff       133 Sep 17 14:33 gdal-3.11.4.tar.gz
-rw-r--r--  1 jimg  staff       133 Sep 17 14:33 gridfields-1.0.5.tar.gz
-rw-r--r--  1 jimg  staff       132 Sep 17 14:33 hdf4-hdf4.3.0.e.tar.gz
-rw-r--r--  1 jimg  staff       133 Sep 17 14:33 hdf5-1.10.10.tar.bz2
-rw-r--r--  1 jimg  staff       131 Sep 17 14:33 jpegsrc.v6b.tar.gz
-rw-r--r--  1 jimg  staff       132 Sep 17 14:33 netcdf-490-e.tar.gz
-rw-r--r--  1 jimg  staff       132 Sep 17 14:33 openjpeg-2.5.3.tar.gz
-rw-r--r--  1 jimg  staff       132 Sep 17 14:33 proj-9.7.0.tar.gz
-rw-r--r--  1 jimg  staff       132 Sep 17 14:33 sqlite-autoconf-3340000.tar.gz
```

To then force git to load all the files, use 'fetch' followed by 'checkout' like
this:
```bash
idoru-2:bes-deps-test jimg$ git lfs fetch --all
18 objects found, done.                                                                             
Fetching all references...
idoru-2:bes-deps-test jimg$ git lfs checkout
Checking out LFS objects: 100% (14/14), 111 MB | 0 B/s, done.                                       
idoru-2:bes-deps-test jimg$ ls -l downloads/
total 224000
-rw-r--r--  1 jimg  staff   7367489 Sep 17 14:40 HDF-EOS2.19v1.00.tar.Z
-rw-r--r--  1 jimg  staff   8782549 Sep 17 14:40 STARE-1.1.0.tar.bz2
-rw-r--r--  1 jimg  staff   2104368 Sep 17 14:40 bison-3.3.tar.xz
-rw-r--r--  1 jimg  staff   4934662 Sep 17 14:40 cfitsio-3.49.tar.gz
...
```

To add a file: ```git add -f big-file.tar.gz``` then,
```git commit -m "...message..."``` followed by
```git push```

## From ChatGPT – a LFS Primer

Here’s a practical, no-nonsense Git LFS guide you can follow right now.

### What it is (30s)

Git LFS stores large/binary files outside your normal Git objects and keeps tiny pointer files in your repo. This avoids giant pushes and weird corruption errors.

### 1) Install (one-time)

```bash
### macOS
brew install git-lfs

### Ubuntu / Debian
sudo apt-get update && sudo apt-get install git-lfs

### Fedora
sudo dnf install git-lfs

### Windows (PowerShell as Admin)
choco install git-lfs
```

Then initialize once:

```bash
git lfs install
```

### 2) Enable in your repo

From the repo root, choose which files to treat as LFS (examples):

```bash
git lfs track "*.nc"
git lfs track "*.h5"
git lfs track "*.bin"
### This creates/updates .gitattributes
git add .gitattributes
git commit -m "chore: track large binaries with Git LFS"
```

### 3) Add, commit, push (normal flow)

```bash
git add path/to/large/file.nc
git commit -m "add data"
git push
```

Tip: make sure `.gitattributes` is committed **before** (or in the same commit as) the large files so they’re captured as LFS pointers.

### 4) Verify it worked

```bash
git lfs status            # shows which files are LFS-tracked
git ls-files -s | head    # LFS files appear as tiny pointer blobs (~130B)
git lfs ls-files          # list LFS objects tracked in repo
```

### 5) If you already committed big files (history rewrite)

If a large file slipped in without LFS (or ≥100 MB on GitHub), migrate it:

```bash
### Include patterns you intend to store in LFS
git lfs migrate import --include="*.nc,*.h5,*.bin" --everything
### Force-push all branches/tags (coordinate with your team!)
git push --all --force
git push --tags --force
```

Safer variant: migrate on a **new branch**, open a PR, and avoid rewriting shared refs:

```bash
git switch -c lfs-migration
git lfs migrate import --include="*.nc,*.h5,*.bin"
git push -u origin lfs-migration
```

### 6) Cloning / pulling with LFS

```bash
git clone <repo>              # downloads LFS pointers + actual LFS files
### If you want a fast/lean clone (skip downloading big files initially):
GIT_LFS_SKIP_SMUDGE=1 git clone <repo>
cd <repo>
git lfs fetch --all
git lfs checkout              # materialize the files you need
```

### 7) Untracking (if you picked a pattern by mistake)

```bash
git lfs untrack "*.bin"
git add .gitattributes
git commit -m "stop tracking *.bin with LFS"
### If those files became pointers already, re-add the real content:
git rm --cached -r .
git checkout -- .
git add .
git commit -m "restore working files"
```

### 8) Useful checks & tips

```bash
git lfs env            # show config and endpoint
git lfs prune          # cleanup old local LFS content
git check-attr -a -- :/path/file.nc   # confirm .gitattributes rules apply
```

* If your host enforces LFS/size limits, make sure your patterns match **before** committing big files.
* LFS uses the same auth/remote as Git; ensure your remote supports LFS (GitHub, GitLab, Bitbucket do).
* Large pushes failing mid-transfer on some networks? Switching to SSH push often helps (unrelated to LFS but handy).

---

##### Minimal “do-this-now” recipe (copy/paste)

```bash
git lfs install
git lfs track "*.nc" "*.h5" "*.bin"
git add .gitattributes
git add path/to/large/files/
git commit -m "Track and add large data with LFS"
git push
```
