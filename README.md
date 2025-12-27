# NGINX Installation Automation (Bash + Ansible)

This repository provides an interactive **Bash wrapper script** and an **Ansible playbook** to install and configure **NGINX** on **RedHat** and **Debian** family Linux systems using **systemd**.

The solution supports both **local users** and **AD/Centrify users**, allowing you to dynamically choose the remote Ansible user and the privilege-escalation method (`sudo` or `dzdo`) at runtime.

---

## 📌 Features

- Interactive Bash script to:
  - Select remote Ansible user
  - Select remote become method (`sudo` or `dzdo`)
- Supports:
  - RHEL / CentOS / AlmaLinux / Rocky Linux
  - Debian / Ubuntu
- Uses **OS native repositories**
- Manages NGINX via **systemd**
- Automatically:
  - Installs NGINX
  - Backs up existing `index.html`
  - Deploys a sample `index.html`
  - Validates NGINX configuration
  - Enables and restarts the service
  - Verifies HTTP response on port `80`

---

## 📂 Repository Structure

```text
.
├── install_nginx.sh        # Interactive Bash wrapper script
├── install_nginx.yml       # Ansible playbook
├── hosts                   # Inventory file
├── index.html              # Sample webpage deployed by playbook
└── README.md               # Documentation
````

---

## ⚙️ Prerequisites

* Ansible installed on the control node
* SSH access to target hosts
* Target hosts must support `systemd`
* Required privileges:

  * `sudo` **or**
  * `dzdo` (Centrify / Delinea environments)

---

## 🚀 How It Works

### 1️⃣ Bash Wrapper Script (`install_nginx.sh`)

The Bash script performs the following actions:

1. Dynamically generates a list of users:

   * `aduser01` → `aduser05`
   * `sandeep`
   * `ansible`
   * `other` (custom input)
2. Prompts for:

   * **Remote Ansible user**
   * **Remote become method** (`sudo` or `dzdo`)
3. Executes the Ansible playbook with the selected options

### Example Prompt Flow

```text
--- Select Remote Ansible User ---
1) aduser01
2) aduser02
3) aduser03
4) aduser04
5) aduser05
6) sandeep
7) ansible
8) other
```

```text
--- Select Remote Become Method ---
1) sudo
2) dzdo
```

---

## ▶️ Usage

### Make the script executable

```bash
chmod +x install_nginx.sh
```

### Run the script

```bash
./install_nginx.sh
```

You will be prompted for:

* SSH password
* Become password (`sudo` or `dzdo`)

---

## 📜 Ansible Playbook Overview (`install_nginx.yml`)

### Supported OS Families

* **RedHat**

  * Uses `yum` for RHEL < 8
  * Uses `dnf` for RHEL ≥ 8
* **Debian**

  * Uses `apt`

### Key Tasks

* Refresh package metadata
* Install NGINX packages
* Backup existing `/var/www/html/index.html`
* Deploy sample `index.html`
* Validate NGINX configuration (`nginx -t`)
* Enable and restart NGINX via systemd
* Wait for port `80`
* Verify HTTP response using Ansible `uri` module

---

## 🔐 Privilege Escalation

The playbook dynamically uses the become method passed from the Bash script:

```yaml
become: true
become_method: "{{ play_become_method }}"
become_user: root
```

Supported methods:

* `sudo`
* `dzdo`

---

## ✅ Verification

After successful execution:

* NGINX service is **enabled and running**
* Port **80** is open
* HTTP status **200** is returned
* Sample webpage is accessible

You can manually verify using:

```bash
curl -I http://<server-ip>
```

---

## 🧪 Sample Output

```text
===== NGINX HTTP RESPONSE =====
Host: server01
HTTP Status: 200
```

---

## ⚠️ Notes & Best Practices

* For AD/Centrify users, ensure:

  * `dzdo` permissions are correctly configured
  * `/tmp` is writable by the remote user
* Existing `index.html` is **never deleted**, only backed up with a timestamp
* The playbook is safe to re-run (idempotent)

---

## 🙌 Author

**Sandeep Reddy Bandela**

Automation | Linux | Ansible | Infrastructure Engineering

```



