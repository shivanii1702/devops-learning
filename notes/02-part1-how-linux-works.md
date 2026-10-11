# Part 1: How the Internet, Servers and Linux Work

## 1. How the internet works

- **Data centers**: buildings full of powerful computers storing information.
- **Submarine fiber-optic cables**: carry most intercontinental traffic. Satellites are too slow (high latency) for the bulk of it.
- **ISP (Internet Service Provider)**: the company you pay (data plan / broadband) to connect to those networks.

## 2. Servers and clients

- **Server**: a computer dedicated to serving one kind of information.
- **Client**: whatever requests it (browser, phone, app).

| Server type | Serves | Examples |
|---|---|---|
| Web server | static content (HTML, images, files) | Nginx, Apache |
| Application server | dynamic logic, per-user responses | Django, Node.js |
| Database server | stored data | PostgreSQL, MySQL |
| Mail server | email | Postfix |
| File server | shared files | NFS, Samba |

**Web vs application server:** a web server hands over files as they are; an application server runs code and builds the response. Instagram/YouTube need app servers, database servers, mail servers and more. A DevOps engineer connects and keeps them running.

## 3. DNS

Computers use IP addresses. **DNS (Domain Name System)** maps names to IPs.

1. Browser asks a DNS server for the IP of `youtube.com`.
2. DNS replies with an IP.
3. Request goes via the ISP to that IP.
4. Server replies with the page.

```bash
getent hosts youtube.com
```

## 4. Application types

- **Stand-alone**: works without internet or database (calculator, kiosk).
- **Web application**: runs over the internet, relies on several servers.

Application support and maintenance = keeping apps stable, monitoring, preventing crashes and connectivity problems.

## 5. Operating systems and why Linux

- OS = core program that lets applications run on hardware.
- Linux created by **Linus Torvalds in 1991**: open source, secure, multitasking, no forced updates.
- Most production servers run Linux, so DevOps starts here.

## 6. Ways to get Linux

- Dual boot / bare metal (my setup)
- WSL2 on Windows
- Virtual machine (VirtualBox, VMware)
- Cloud VM (AWS EC2, Azure, GCP)
- Vagrant (HashiCorp) to script VMs

## 7. Kernel, shell, bootloader

| Part | Job | Analogy |
|---|---|---|
| Kernel | core of the OS, written in C, manages processes, memory, hardware | engine |
| Shell | interface where you type commands to the kernel | steering wheel |
| Bootloader (GRUB) | first program that starts the OS | alarm clock |

**Layers:** User -> Applications -> Shell -> Kernel -> Hardware.
Users never touch hardware directly; the kernel does it safely.

**Boot order:** power on -> firmware (UEFI/BIOS) -> GRUB -> kernel -> PID 1 (systemd) -> services.

## 8. Remote access basics

- **SSH**: encrypted command-line access to a remote machine (port 22).
- **RDP**: remote desktop (graphical), mostly Windows.
- Being able to connect from anywhere is how DevOps engineers troubleshoot servers.

## 9. Checking hardware and resources

```bash
free -h     # memory
df -h       # disk space
top         # live processes and CPU (q to quit)
```

## 10. Directory tree (recap)

`/` is the root. `/home` users, `/bin` binaries, `/etc` config, `/var` variable data (`/var/log` logs), `/dev` and `/proc` devices and processes as files.

## 11. Processes

- Each process has a **PID**. The first process started at boot is **PID 1**.
- Background processes keep the system working.

| State | Letter | Meaning |
|---|---|---|
| Running | R | using CPU |
| Sleeping | S | waiting |
| Stopped | T | paused |
| Zombie | Z | finished, parent has not collected exit status |

Zombies use almost no resources (a process-table entry); they only matter in large numbers.

## 12. Hands-on commands

```bash
cat /etc/os-release | head -n 3
uname -r
echo $SHELL
ps -p 1 -o pid,comm
ls /
ls /boot
ps -eo stat | cut -c1 | sort | uniq -c
free -h
df -h /
top
```

## Self-check

1. Web server vs application server?
2. Four layers between user and hardware?
3. What is PID 1 on my machine and why does it matter?
