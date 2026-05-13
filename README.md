# Docker Container Communication Lab (TCP + ICMP + SSH)

## Project Overview

This project demonstrates communication between two Docker containers using:

- TCP Communication
- ICMP (Ping) Communication
- SSH Communication

Both containers are connected using the same custom Docker network.

---

# Architecture

```text
+----------------------+
|   Container server   |
|----------------------|
| TCP Server           |
| Ping                 |
| SSH Server           |
+----------------------+
           |
           |
      Docker Network
         (mynet)
           |
           |
+----------------------+
|   Container client   |
|----------------------|
| TCP Client           |
| Ping                 |
| SSH Client           |
+----------------------+
```

---

# Server Details

```text
Your-Server-IP
```

---

# Step 1: Connect to Server

```bash
ssh azureuser@Your-Server-IP
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
ssh azureuser@Your-Server-IP
```

Verify Docker:

```bash
docker --version
```

---

# Step 3: Create Docker Network

## Terminal 1: Create Docker Network

```bash
docker network create mynet
```

Check network:

```bash
docker network ls
```

---

# Step 4: Create Containers

## Terminal 1: Create server Container

```bash
docker run -dit --name server --network mynet ubuntu bash
```

---

## Terminal 2: Create client Container

```bash
docker run -dit --name client --network mynet ubuntu bash
```

Check running containers:

```bash
docker ps
```

---

# Step 5: Install Required Packages

## Terminal 1: Install Packages in server

```bash
docker exec server apt update
docker exec server apt install -y iputils-ping netcat-openbsd openssh-server openssh-client nano
```

---

## Terminal 2: Install Packages in client

```bash
docker exec client apt update
docker exec client apt install -y iputils-ping netcat-openbsd openssh-client
```

---

# Step 6: TCP Communication Test

## Terminal 1: Start TCP Server

Open server container:

```bash
docker exec -it server bash
```

Start TCP server:

```bash
nc -l -p 1234
```

---

## Terminal 2: Connect TCP Client

Reconnect to server:

```bash
ssh azureuser@Your-Server-IP
```

Open client container:

```bash
docker exec -it client bash
```

Connect client to server:

```bash
nc server 1234
```

Send message:

```text
hello from client
```

Message will appear in server terminal.

TCP communication successful.

---

# Step 7: Configure SSH in server

## Terminal 1: Open server Container

```bash
docker exec -it server bash
```

Set root password:

```bash
passwd root
```

Example password:

```text
123456
```

Enable SSH root login:

```bash
sed -i 's/#PermitRootLogin prohibit-password/PermitRootLogin yes/' /etc/ssh/sshd_config
```

Enable password authentication:

```bash
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

Exit from server container:

```bash
exit
```

---

# Step 8: SSH Communication Test

## Terminal 2: Open client Container

```bash
docker exec -it client bash
```

SSH into server:

```bash
ssh root@server
```

First time type:

```text
yes
```

Enter password:

```text
123456
```

Successful login output:

```text
root@server:~#
```

SSH communication successful.

---

# Step 9: ICMP (Ping) Communication Test

## Terminal 2: Ping server from client

```bash
ping server
```

Expected output:

```text
64 bytes from server
```

Stop ping:

```text
CTRL + C
```

Exit SSH session:

```bash
exit
```

Exit client container if needed:

```bash
exit
```

---

## Terminal 1: Ping client from server

Open server container:

```bash
docker exec -it server bash
```

Run ping:

```bash
ping client
```

Expected output:

```text
64 bytes from client
```

Stop ping:

```text
CTRL + C
```

ICMP communication successful.

---

# Final Verification

| Communication Type | Status |
|--------------------|--------|
| TCP Communication  | Successful |
| ICMP Communication | Successful |
| SSH Communication  | Successful |

---

# Real World Use Case

This same concept is used in production environments.

| Container | Role |
|------------|------|
| frontend   | React Application |
| backend    | Node.js API |
| database   | MySQL Database |

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
docker rm -f server client
```

Remove network:

```bash
docker network rm mynet
```

Verify cleanup:

```bash
docker ps -a
docker network ls
```

---

# Outcome

After completing this lab:

- You understand Docker custom networking
- You tested TCP communication between containers
- You verified ICMP (Ping) communication
- You configured SSH communication between containers
- You learned container-to-container communication concepts used in real-world environments
