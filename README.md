# SSH-WINDOWS-DEDICATED-USER

## How to Enable SSH on Windows With a Separate Username and Password

This guide explains how to enable SSH on Windows 10/11 using a dedicated local Windows account named `sshuser`. This account has its own password, separate from your normal Windows account and Windows Hello PIN.

## Requirements

- Windows 10 or Windows 11
- Administrator access to the Windows computer
- Another computer with an SSH client
- Both computers on the same network for the initial setup

## 1. Install OpenSSH Server

On the Windows computer you want to access, open **PowerShell as Administrator** and run:

```powershell
Add-WindowsCapability -Online -Name OpenSSH.Server~~~~0.0.1.0
```

Wait for installation to complete.

## 2. Create a Separate SSH User

Run the following in Administrator PowerShell:

```powershell
$password = Read-Host "Enter a password for sshuser" -AsSecureString

New-LocalUser -Name "sshuser" -Password $password -Description "Dedicated SSH account"
```

Enter a strong password when prompted. This creates a separate local Windows account. The password is not your Windows Hello PIN or your regular account password.

Do not add this account to the Administrators group unless administrative access is specifically required.

## 3. Start the SSH Server

Run:

```powershell
Start-Service sshd
Set-Service -Name sshd -StartupType Automatic
```

Verify the service:

```powershell
Get-Service sshd
```

The status should be `Running`.

## 4. Check the Firewall

OpenSSH normally creates a firewall rule during installation. Check it with:

```powershell
Get-NetFirewallRule -Name "OpenSSH-Server-In-TCP"
```

If the rule exists and is enabled, no additional firewall configuration is normally needed for local-network access.

## 5. Find the Windows PC's IP Address

Run:

```powershell
ipconfig
```

Find the IPv4 address of your active network adapter. For example:

```text
192.168.1.50
```

This is an example address. Use the actual IPv4 address shown on your computer.

## 6. Connect From Another Computer

Open a terminal on your other PC and run:

```bash
ssh sshuser@192.168.1.50
```

Replace the example IP with the real address.

On the first connection, SSH may ask you to verify the server's host key. Confirm the fingerprint through a trusted method before accepting it.

When prompted for a password, enter the password you created for `sshuser`. The characters will not appear while you type.

## 7. Test the Connection

After connecting, run:

```powershell
whoami
```

You should see an account identifier containing `sshuser`.

You can also test basic commands:

```powershell
hostname
Get-Date
Get-Location
```

## Security Notes

- Use a strong, unique password.
- Keep Windows and OpenSSH updated.
- Do not expose port 22 directly to the public internet.
- For remote access across different networks, consider Tailscale or another secure private network.
- The dedicated account has its own permissions. It does not automatically grant access to every file belonging to your normal Windows account.
- SSH provides terminal access, not graphical desktop control.

## Disable SSH When It Is Not Needed

To stop the server temporarily:

```powershell
Stop-Service sshd
```

To stop it from starting automatically:

```powershell
Set-Service -Name sshd -StartupType Disabled
```

To enable it again later:

```powershell
Set-Service -Name sshd -StartupType Automatic
Start-Service sshd
```

## Official Documentation

Microsoft OpenSSH setup guide:

https://learn.microsoft.com/en-us/windows-server/administration/openssh/openssh_install_firstuse

This guide uses Microsoft's built-in OpenSSH implementation for Windows.
