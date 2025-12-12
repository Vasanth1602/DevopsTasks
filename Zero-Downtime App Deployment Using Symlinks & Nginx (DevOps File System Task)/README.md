# 📘 **README.md — Zero-Downtime Deployment Using Symlinks (DevOps FS Task)

---

# 🚀 **Zero-Downtime App Deployment Using Symlinks & Nginx (DevOps File System Task)**

This project demonstrates a **real-world DevOps deployment strategy** using:

* Linux File System Standards (FHS)
* Symlink-based version switching
* Nginx root mapping
* Zero-downtime deployments
* Safe backup & rollback
* Deployment automation using a custom CLI tool

This method is used by major companies such as **GitHub, Shopify, Airbnb, Netflix**, and it is also the foundation for tools like **Capistrano, Ansible deploy modules, and CI/CD pipelines**.

---

# 📌 **1. What This Task Is About**

This task teaches **how to deploy multiple versions of an application** by:

* Storing each version separately
* Using a **symlink named `current`** to point to the active version
* Using Nginx to serve the symlink path
* Switching versions instantly and safely
* Backing up the current version before deployment
* Automating deployment with a CLI tool

This is exactly how **zero-downtime production deployments** work in DevOps.

---

# 🎯 **2. Why This Task Matters (Real DevOps Use Case)**

### ✔ Production applications cannot stop during deployment

Downtime leads to user loss, errors, and failed transactions.

### ✔ Symlink deployments switch versions instantly

Changing a symlink takes **less than 1 millisecond**.

### ✔ Rollback is critical

If a new version fails, you need **instant rollback** without losing time.

### ✔ Config files must live outside the application code

Following Linux FHS standards ensures predictable behavior.

### ✔ Logs, backups, and releases must be separated

Just like real production systems.

This task trains you in **real DevOps responsibilities**.

---

# 🧠 **3. How It Works (High-Level Explanation)**

```
/var/www/myapp
│
├── releases
│   ├── v1
│   ├── v2
│   └── v3
│
├── current  →  symlink pointing to one release (v1 or v2)
│
├── logs
│
└── backups
```

Nginx always serves:

```
root /var/www/myapp/current;
```

When you deploy a new version:

```
current → releases/v2
```

A symlink flip = zero downtime.

---

# 🖼 **4. Deployment Architecture Diagram**

```
                ┌────────────────────────────┐
                │        /var/www/myapp       │
                ├────────────────────────────┤
                │ releases/                  │
                │   ├── v1                   │
                │   ├── v2                   │
                │   └── v3                   │
                │                            │
                │ current → releases/v2      │  ← Active version
                │                            │
                │ logs/                      │
                │ backups/                   │
                └────────────────────────────┘
                             │
                             ▼
                 ┌──────────────────────┐
                 │      Nginx Server     │
                 │ root: /var/www/myapp/current
                 └──────────────────────┘
                             │
                             ▼
                Browser → http://localhost:PORT
```

---

# 🧪 **5. Full Step-by-Step Commands**

## **STEP 1 — Create directory structure**

```bash
mkdir -p /var/www/myapp/releases
mkdir -p /var/www/myapp/logs
mkdir -p /etc/myapp
mkdir -p /var/backups/myapp
```

---

## **STEP 2 — Create Version 1**

```bash
mkdir -p /var/www/myapp/releases/v1
echo "<h1>MyApp v1</h1>" > /var/www/myapp/releases/v1/index.html
```

---

## **STEP 3 — Create Version 2**

```bash
mkdir -p /var/www/myapp/releases/v2
echo "<h1>MyApp v2</h1>" > /var/www/myapp/releases/v2/index.html
```

---

## **STEP 4 — Create the `current` symlink**

```bash
ln -sfn /var/www/myapp/releases/v1 /var/www/myapp/current
```

---

## **STEP 5 — Configure Nginx**

Create file:

```bash
cat <<EOF > /etc/nginx/conf.d/myapp.conf
server {
    listen 8080;
    root /var/www/myapp/current;
    index index.html;
}
EOF
```

Reload nginx:

```bash
nginx -s reload
```
```

---

## **STEP 6 — Backup current version (before deployment)**

```bash
cp -r /var/www/myapp/current /var/backups/myapp/backup_$(date +%F)
```

---

## **STEP 7 — Deploy Version 2 (zero downtime)**

```bash
ln -sfn /var/www/myapp/releases/v2 /var/www/myapp/current
nginx -s reload
```

---

## **STEP 8 — Rollback Deployment**

```bash
ln -sfn /var/www/myapp/releases/v1 /var/www/myapp/current
nginx -s reload
```

---

## **STEP 9 — Create a Deployment CLI Tool**

```bash
cat <<EOF > /usr/local/bin/myappctl
#!/bin/bash
ln -sfn /var/www/myapp/releases/$1 /var/www/myapp/current
nginx -s reload
echo "Deployed version $1"
EOF
```

Make executable:

```bash
chmod +x /usr/local/bin/myappctl
```

Deploy via CLI:

```bash
myappctl v2
```

---

# 📌 **6. Case Study: How Companies Use This in Production**

### 🏢 Real example: GitHub

GitHub stores each release under:

```
releases/2025-12-12-14-00/
```

Then updates symlink:

```
current → releases/2025-12-12-14-00
```

Nginx/Unicorn loads the new version without interrupting users.

### 🏢 Shopify / Rails Apps

Uses Capistrano (which internally uses `ln -sfn`).

### 🏢 Netflix

Uses symlink-based switching for certain microservices.

### 🏢 Cloud providers (Heroku, Dokku)

They also use versioned folder structures internally.

---

# 🧠 **7. What You Learn From This Task**

### ✔ Linux File System layout (`/var`, `/etc`, `/usr`)

### ✔ Symlinks and how they point to active versions

### ✔ Zero-downtime deployment

### ✔ Rollback strategy

### ✔ Backup strategy

### ✔ Nginx root mapping

### ✔ Custom DevOps CLI tooling

### ✔ How real CI/CD works internally

This one task alone gives **deep DevOps understanding**.

---

# 📚 **8. Notes for Future Revision**

* Deployment = switching which version “current” points to
* Never edit live files directly → always deploy new version
* `/etc` = config
* `/var/www` = app code
* `/var/backups` = backups
* `/usr/local/bin` = admin tools
* Symlinks (`ln -sfn`) ensure safety & consistency
* Reloading nginx applies changes instantly

---

# 🎉 **9. Final Output in Browser**

After deployment, access:

```
http://localhost:8080
```

(or another mapped port like 9090 if using Docker)

---

# ✔ Done