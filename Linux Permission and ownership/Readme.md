# **File Types**
1. Regular File (`-`)
    * Code files (`.py`, `.js`, `.go`)
    * Config files (`nginx.conf`,`.env`)
    * Log files
    * Shell scripts (`.sh`)
2. Directory (`d`)
    * A directory is just a folder.
3. Symbolic Link (`l`)
    * A symlink is a pointer to another file.
4. Socket (`s`)
    * IPC (inter-process communication) file.
5. Block Device (`b`)
    * Represents storage devices (HDD, SSD, partitions).

---

# **Permission Types**  
1. READ (`r`)
* For Files: *Read = view content*
* For Directories: *Read = list files*
2. WRITE (`w`)
* For Files: *Write = modify content*
* For Directories: *Write = create / delete / rename files*
3. EXECUTE (`x`)
* For Files: *Execute = run as program*
* For Directories: *Execute = enter the directory*

---

# **Three Permission Classes**
1. User (u) — OWNER
2. Group (g) — GROUP OWNER
3. Others (o) — EVERYONE ELSE
*Example:* ```-rw-r----- 1 ubuntu devops 1234 config.env```

```
Part            	       Meaning
  -	              Regular file
 rw-	          User can read & write
 r--	           Group can only read
 ---	          Others have no access

```
# **Change Permissions - ``chmod`` **
`chmod` = change mode
It changes read (r), write (w), execute (x) permissions for:
* User (u)
* Group (g)
* Others (o)

1. *Symbolic Mode*
    Format: ``` chmod [who][+|-|=][permissions] file```
    who:
    u → user
    g → group
    o → others
    a → all
    Example:
    ``chmod o-w config.env``: Remove write permission from others
    ``chmod u=rwx,g=rx,o= deploy.sh``: Set exact permissions

2. *Numeric Mode*
        Permission values:
    ```
    Permission	   Value
        r                   4
        w                   2
        x                   1
    ```
    Add them:
    `rwx` = 7
    `rw-` = 6
    `r--` = 4
    `---` = 0

    **DevOps MUST-MEMORIZE MODES**
    ```
    Mode	    Use Case
    755	Directories, executables
    644	Config files, code
    600	Secrets, SSH keys
    775	Shared directories
    700	Private scripts
    ```

---

# **Ownership (`chown` & `chgrp`)**

Permissions alone are not enough. **Ownership decides whose permissions apply first.**

## What is Ownership?

Every file/directory has:

* Owner (User)
* Group

Check with:

```
ls -l
```

Example:

```
-rw-r----- 1 ubuntu devops 2048 config.env
```

* Owner → `ubuntu`
* Group → `devops`

---

## A. `chown` — Change Owner

### Syntax

```
chown user file
chown user:group file
```

### Common DevOps examples

✔ Change owner to app user

```
chown appuser app.log
```

✔ Change owner + group

```
chown appuser:appgroup app/
```

✔ Recursive (USED ALL THE TIME)

```
chown -R appuser:appgroup /var/www/app
```

---

## B. `chgrp` — Change Group Only

### Syntax

```
chgrp group file
```

### DevOps use

```
chgrp docker /var/run/docker.sock
```

Useful when:

* User already correct
* Only group access needed

---



# **`umask` (Default Permissions) — Quick Summary**

`umask` sets which permission bits are removed by default when creating files/directories.

- Files start from `666` (no execute by default)
- Directories start from `777`
- Effective perms = Base − `umask`

## Check current umask
```
umask
```
Common values: `022`, `002`, `077`

## Quick reference
- `022` → Files: 644, Dirs: 755 (standard system default)
- `002` → Files: 664, Dirs: 775 (good for shared dev groups)
- `077` → Files: 600, Dirs: 700 (strict/private)

## Set `umask`
Temporary (current shell):
```
umask 022
```
Persist (example): add to `~/.profile` or service unit’s environment.

## DevOps tips
- CI/CD runners: use `002` for group-collab workspaces
- Secrets directories: use `077`
- Docker ENTRYPOINT: set `umask` early if you need predictable defaults

---