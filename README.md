# SHH-WINDOWS
How to enable ssh on windows, easy step by step cmd
To enable SSH access on your Windows 11 computer, you need to install OpenSSH Server, start the service, and connect from your other PC.

## 1. Install OpenSSH Server

Open PowerShell as Administrator on the Windows computer you want to access, then run:

```
Add-WindowsCapability -Online -Name OpenSSH.Server~~~~0.0.1.0
```

Wait for the installation to finish.

## 2. Start SSH and enable automatic startup

Run these commands in the same Administrator PowerShell window:

```
Start-Service sshd
Set-Service -Name sshd -StartupType Automatic
```

This starts SSH now and makes it start automatically whenever Windows boots. The installer normally creates the required firewall rule for port 22.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://learn.microsoft.com\&sz=32)

Microsoft Learn



## 3. Find your Windows computer's IP address

Run:

```
ipconfig
```

Find the active network adapter and its IPv4 Address, for example `192.168.1.50`.

Also find your Windows username:

```
whoami
```

It will return something like `desktop-abc\ugur`.

## 4. Connect from your other PC

From another Windows PC, open PowerShell. From Arch Linux, open a terminal.

Run:

```
ssh YOUR_USERNAME@192.168.1.50
```

Replace `YOUR_USERNAME` with your Windows username and the IP with the actual address. If both computers are on the same network, this should work once the firewall and service are configured.

The first connection asks you to verify the server's host key. After verifying it, type `yes`, then enter your Windows account password.

## 5. Verify SSH is running

On the Windows computer, run:

```
Get-Service sshd
```

The status should be `Running`.

Security: Start by connecting over your home network. If you want access from another network, use Tailscale rather than exposing port 22 directly to the internet.

Official instructions: [Microsoft's OpenSSH setup guide](https://learn.microsoft.com/en-us/windows-server/administration/openssh/openssh_install_firstuse?utm_source=chatgpt.com).
