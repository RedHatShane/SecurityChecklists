# Rocky Linux 9 SSH Security Checklist

## Assumptions:
You are running a new Rocky Linux server with root access and public key SSH authentication.

### Run updates and install packages:
`dnf update`

`dnf install firewalld`

### Create non-root SSH user and copy SSH keys:
`adduser ssh_user`

`passwd ssh_user`

`usermod -aG wheel ssh_user`

`mkdir /home/ssh_user/.ssh`

`chown -R ssh_user:ssh_user /home/ssh_user/.ssh`

**BEFORE CONTINUING:** Leave initial SSH session open and verify you can connect as the new user and use sudo

### Configure firewallD and SELinux with an alternate SSH port
`systemctl start firewalld`

`systemctl enable firewalld`

`firewall-cmd --add-port=2222/tcp --permanent`

`firewall-cmd --reload`

`semanage port -a -t ssh_port_t -p tcp 2222`

### Update SSHD config (remove comments as needed) and reload service
`vi /etc/ssh/sshd_config`

```
  PermitRootLogin no
	Port 2222
	LoginGraceTime 1m
	MaxAuthTries 5
	MaxSessions 8
	PasswordAuthentication no
	PermitEmptyPasswords no
 ```
  
`systemctl reload sshd`

**BEFORE CONTINUING:** Leave existing SSH session(s) open and verify you can connect as the new user on the new port. Additionally, verify that the root user cannot log in via SSH.

### Remove default SSH port (22) from firewalld and SELinux configurations
`firewall-cmd --remove-service=ssh --permanent`

`firewall-cmd --reload`