# Permission & Ownership Summary

## Users & Groups
- **admin1**: DevOps / Infra owner
- **dev1**: Application developer
- **developers**: Group for deployment access
- **devops**: Group for infrastructure
- **www-data**: NGINX runtime user

## Ownership
- `/var/www/myapp`: `admin1:devops` (Admin owns infra)
- `/var/www/myapp/current`: `admin1:developers` (Devs collaborate here)
- `/var/www/myapp/logs`: `admin1:developers` (Logs access)

## Permissions
- `/var/www/myapp`: `750` (rwx for admin, r-x for devops)
- `/var/www/myapp/current`: `770` (rwx for admin and devs)
- `/var/www/myapp/logs`: `750` (rwx for admin, r-x for devs)
- `/var/www/myapp/backups`: `700` (rwx for admin only)

## NGINX Access
NGINX (`www-data`) is added to the `developers` group to read application files.
- `usermod -aG developers www-data`
- `chmod g+s /var/www/myapp/current` (SetGID for inheritance)
