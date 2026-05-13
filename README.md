# Docker Container Communication Lab (TCP + ICMP + SSH)

## Project Overview

This project demonstrates communication between two Docker containers using:

* TCP Communication
* ICMP (Ping) Communication
* SSH Communication

Both containers are connected using the same custom Docker network.

---

# Architecture

```text
+-------------------+
|   Container c1    |
|-------------------|
| TCP Server        |
| Ping              |
| SSH Client        |
+-------------------+
          |
          |
     Docker Network
        (mynet)
          |
          |
+-------------------+
|   Container c2    |
|-------------------|
| TCP Client        |
| Ping              |
| SSH Server        |
+-------------------+
```

---

# Server Details

Server IP:

```text
20.10.189.4
```

---

# Step 1: Connect to Server

```bash
ssh azureuser@20.10.189.4
```

---

# Step 2: Install Docker

```bash
sudo apt update
sudo apt install -y docker.io
sudo systemctl enable docker
sudo systemctl start docker
sudo usermod -aG docker $USER
```

Logout:

```bash
exit
```

Reconnect:

```bash
ssh azureuser@20.10.189.4
```

Verify Docker:

```bash
docker --version
```

---

# Step 3: Create Docker Network

```bash
docker network create mynet
```

Check network:

```bash
docker network ls
```

---

# Step 4: Create Containers

```bash
docker run -dit --name c1 --network mynet ubuntu bash
docker run -dit --name c2 --network mynet ubuntu bash
```

Check containers:

```bash
docker ps
```

---

# Step 5: Install Required Packages

## Install Packages in c1

```bash
docker exec c1 apt update
docker exec c1 apt install -y iputils-ping netcat-openbsd openssh-client
```

---

## Install Packages in c2

```bash
docker exec c2 apt update
docker exec c2 apt install -y iputils-ping netcat-openbsd openssh-server openssh-client nano
```

---

# Step 6: TCP Communication Test

## Terminal 1

Open c1:

```bash
docker exec -it c1 bash
```

Start TCP server:

```bash
nc -l -p 1234
```

---

## Terminal 2

Connect to server again:

```bash
ssh azureuser@20.10.189.4
```

Open c2:

```bash
docker exec -it c2 bash
```

Connect TCP client to c1:

```bash
nc c1 1234
```

Send message:

```text
hello from c2
```

Message will appear in c1 terminal.

TCP communication successful.

---

# Step 7: Configure SSH in c2

Open c2:

```bash
docker exec -it c2 bash
```

Set root password:

```bash
passwd root
```

Example password:

```text
123456
```

---

# Step 8: Enable Root Login and Password Authentication

```bash
sed -i 's/#PermitRootLogin prohibit-password/PermitRootLogin yes/' /etc/ssh/sshd_config

sed -i 's/#PasswordAuthentication yes/PasswordAuthentication yes/' /etc/ssh/sshd_config
```

Restart SSH service:

```bash
service ssh restart
```

Check SSH status:

```bash
service ssh status
```

Exit from c2:

```bash
exit
```

---

# Step 9: SSH Communication Test

Open c1:

```bash
docker exec -it c1 bash
```

SSH into c2:

```bash
ssh root@c2
```

Type:

```text
yes
```

Enter password:

```text
123456
```

Successful login output:

```text
root@56250ee3d44f:~#
```

SSH communication successful.

---

# Step 10: ICMP (Ping) Communication Test

Inside c2:

```bash
ping c1
```

Expected output:

```text
64 bytes from c1
```

Stop ping:

```text
CTRL + C
```

Exit from c2:

```bash
exit
```

Inside c1:

```bash
ping c2
```

Expected output:

```text
64 bytes from c2
```

Stop ping:

```text
CTRL + C
```

ICMP communication successful.

---

# Final Verification

| Communication Type | Status       |
| ------------------ | ------------ |
| TCP Communication  | ✅ Successful |
| ICMP Communication | ✅ Successful |
| SSH Communication  | ✅ Successful |

---

# Real World Use Case

This same concept is used in production environments.

Example:

| Container | Role              |
| --------- | ----------------- |
| frontend  | React Application |
| backend   | Node.js API       |
| database  | MySQL Database    |

Communication flow:

```text
frontend → backend
backend → database
```

All containers communicate using the same Docker network.

---

# Cleanup

Exit containers:

```bash
exit
```

Remove containers:

```bash
docker rm -f c1 c2
```

Remove network:

```bash
docker network rm mynet
```

Verify cleanup:

```bash
docker ps -a
docker network ls
