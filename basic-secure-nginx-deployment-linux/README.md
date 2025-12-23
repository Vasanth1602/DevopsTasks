# 🔐 Secure NGINX Deployment Using Linux Users, Groups & Permissions (DevOps Task)

This project demonstrates a **real-world DevOps scenario** where an application is securely hosted using **NGINX** while enforcing **Linux user management, group collaboration, file ownership, and permissions**.

The goal is to ensure:
- Developers can deploy code safely
- NGINX can serve files with least privilege
- Infrastructure remains protected from accidental or malicious changes

---

## 📌 Why This Project Exists

In real production environments:
- Developers should NOT use `root`
- Web servers should NOT have write access
- Infrastructure and application responsibilities must be separated

This task simulates how a **DevOps engineer designs a secure Linux environment** for application hosting.

---

## 🧠 Real-World Scenario

- Multiple developers deploy application code
- NGINX serves the application
- Only DevOps/admin can manage infrastructure
- Logs are readable but protected
- Backups are restricted

---

## 🧱 Architecture Overview

```

Browser
|
v
NGINX (www-data user)
|
v
/var/www/myapp/current

```

Roles:
- **admin1** → DevOps engineer (infra owner)
- **dev1** → Developer (deploys code)
- **www-data** → NGINX service user

---

## 👥 Users & Groups Design

| Entity | Purpose |
|------|--------|
| admin1 | DevOps / Infra owner |
| dev1 | Application developer |
| developers | Group for deployment access |
| devops | Group for infrastructure |
| www-data | NGINX runtime user |

---

## 📂 Directory Structure

```

/var/www/myapp
├── current   → Application code
├── logs      → Application / NGINX logs
└── backups   → Restricted backups

```

---

## 🔐 Ownership Design

```bash
chown -R admin1:devops /var/www/myapp
chown -R admin1:developers /var/www/myapp/current
chown -R admin1:developers /var/www/myapp/logs
```

**Logic:**

* Admin owns infrastructure
* Developers collaborate on code
* NGINX never owns files

---

## 🔑 Permission Design

```bash
chmod 750 /var/www/myapp
chmod 770 /var/www/myapp/current
chmod 750 /var/www/myapp/logs
chmod 700 /var/www/myapp/backups
```

| Directory | Admin | Developers | Others |
| --------- | ----- | ---------- | ------ |
| myapp     | rwx   | r-x        | ---    |
| current   | rwx   | rwx        | ---    |
| logs      | rwx   | r-x        | ---    |
| backups   | rwx   | ---        | ---    |

---

## 🌐 NGINX Access Configuration

NGINX runs as `www-data` and must only **read** application files.

```bash
usermod -aG developers www-data
chmod -R g+rX /var/www/myapp/current
chmod g+s /var/www/myapp/current
```

SetGID ensures all deployed files inherit the correct group.

---

## ⚙️ NGINX Configuration

**`/etc/nginx/sites-available/myapp`**

```nginx
server {
    listen 80;
    server_name _;

    root /var/www/myapp/current;
    index index.html;

    location / {
        try_files $uri $uri/ =404;
    }
}
```

Enable the site:

```bash
ln -s /etc/nginx/sites-available/myapp /etc/nginx/sites-enabled/
rm /etc/nginx/sites-enabled/default
nginx -t
nginx
```

---

## 🧪 Validation & Testing

### As Developer

```bash
touch /var/www/myapp/current/test.html   # ✅ allowed
rm -rf /var/www/myapp/backups            # ❌ denied
```

### As NGINX

```bash
curl http://localhost
```

### Intentional Failure Test

```bash
chmod 700 /var/www/myapp/current
curl http://localhost   # ❌ 403 Forbidden
chmod 770 /var/www/myapp/current
```

---

## 🚀 Future Improvements

* Git-based deployment
* Dockerfile for containerization
* Docker-Compose setup
* Log rotation
* HTTPS with TLS

---

## 🧑‍💻 Author

**Vasanth A**
Aspiring DevOps Engineer | Linux | NGINX | Docker
