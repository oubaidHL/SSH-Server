---

# 🧰 Secure SSH Demo Environment (Docker, Fail2ban, Lynis)

This project contains a **secure SSH server** and a **client container** used for testing:

* SSH key authentication
* Password authentication
* Fail2ban blocking
* MOTD security banner
* Lynis audit
* Admin user login
* ngrok exposure option

---

# 📁 Project Structure

```
SSH Server/
│   docker-compose.yml
│
├───client/
│       Dockerfile
│       id_ed25519
│       id_ed25519.pub
│
└───ssh_server/
        Dockerfile
        id_ed25519.pub
```

---

# 🔐 1. Generate SSH Keys

You must generate a key pair before building the images.

---

## 🖥 Windows (PowerShell)

Run inside the **client folder**:

```powershell
cd client
ssh-keygen -t ed25519 -C "demo-key" -f id_ed25519
```

You will get:

```
id_ed25519
id_ed25519.pub
```

Copy the public key into the SSH server folder:

```powershell
Copy-Item id_ed25519.pub ..\ssh_server\
```

---

## 🐧 Linux / macOS

Run inside the **client folder**:

```bash
cd client
ssh-keygen -t ed25519 -C "demo-key" -f id_ed25519
```

Copy pub key:

```bash
cp id_ed25519.pub ../ssh_server/
```

---

# 🚀 2. Start the environment

From the **root project directory**:

```powershell
docker compose up -d --build
```

This will start:

* **ssh-server** → secure Debian server
* **client** → preconfigured client with ssh + lynis

---

# 🧪 3. Using the Client Container

Enter the client shell:

```powershell
docker exec -it client bash
```

Now you can:

### 🔑 Login with SSH key (root):

```bash
ssh -i /root/.ssh/id_ed25519 root@ssh-server
```

### 🔐 Login with password (admin):

```bash
ssh admin@ssh-server
```

Password:

```
RaNdOmPaSsWoRd
```

---

# 🔥 4. Using the SSH Server Container

Enter server shell:

```powershell
docker exec -it ssh-server bash
```

Check Fail2ban status:

```bash
fail2ban-client status
fail2ban-client status sshd
```

Check banned IPs:

```bash
fail2ban-client status sshd
```

Unban client (if needed):

```bash
fail2ban-client set sshd unbanip 172.19.0.X
```

---

# 🛡 5. Test Fail2ban (Auto-Ban After 5 Attempts)

Inside the **client container**:

```bash
ssh admin@ssh-server
```

Enter WRONG password 5 times.

Fail2ban should ban the client IP.

Check from server:

```bash
fail2ban-client status sshd
```

---

# 🧾 6. Run Lynis Audit

From the client container:

```bash
lynis audit system --quick
```

---

# 🌍 7. Expose SSH Server via Internet (Optional)

With ngrok installed:

```bash
ngrok tcp 2222
```

You will get:

```
tcp://2.tcp.eu.ngrok.io:18063 → localhost:2222
```

Connect from anywhere:

```bash
ssh -p 18063 admin@2.tcp.eu.ngrok.io
```

---

# 🎯 8. Summary of Login Methods

### SSH Server (local host):

```
ssh -p 2222 admin@localhost
```

### Using SSH key:

```
ssh -p 2222 -i client/id_ed25519 root@localhost
```

### From client container:

```
ssh admin@ssh-server
ssh -i /root/.ssh/id_ed25519 root@ssh-server
```

---

# 🧩 This Demo Includes

✔ SSH server (Debian 12)
✔ Fail2ban (5 retries → 30 min ban)
✔ rsyslog + auth.log working
✔ Lynis security audit
✔ Admin user with password
✔ SSH key login for root
✔ Custom MOTD + ASCII dog
✔ Docker Compose network

---
