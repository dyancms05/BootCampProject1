# Linux-SysAdmin-Fundamentals
## Ensure/Double Check Permissions on Sensitive Files
### Permissions on /etc/shadow should allow only root read and write access.
Command to inspect permissions: `ls -l /etc/shadow`
Command to set permissions: `sudo chmod 600 /etc/shadow`

### Permissions on /etc/gshadow should allow only root read and write access.
Command to inspect permissions: `ls -l /etc/gshadow`
Command to set permissions: `sudo chmod 600 /etc/gshadow`

### Permissions on /etc/group should allow root read and write access, and allow everyone else read access only.
Command to inspect permissions: `ls -l group`
Command to set permissions: `sudo chmod 644 /etc/gshadow`

### Permissions on /etc/passwd should allow root read and write access, and allow everyone else read access only.
Command to inspect permissions: `ls -l /etc/passwd`
Command to set permissions: `sudo chmod 644 /etc/passwd`

## Create User Accounts
### Add user accounts for sam, joe, amy, sara, and admin.
Command to add each user account (include all five users):
`sudo useradd sam`
`sudo useradd joe`
`sudo useradd amy`
`sudo useradd sara`
`sudo useradd admin`

Command to add admin to the sudo group: `sudo usermod -aG sudo admin`

## Create User Group and Collaborative Folder
### Add an engineers group to the system.

Command to add group: `sudo addgroup engineers`

### Add users sam, joe, amy, and sara to the managed group.

Command to add users to engineers group (include all four users):
`sudo usermod -aG engineers sam`
`sudo usermod -aG engineers joe`
`sudo usermod -aG engineers amy`
`sudo usermod -aG engineers sara`

Create a shared folder for this group at /home/engineers.

Command to create the shared folder: `sudo mkdir engineers`

Command to change ownership of engineer's shared folder to engineer group:
`sudo chown root:engineers engineers`

## Lynis Auditing
Command to install Lynis: `sudo apt install lynis`
Command to see documentation and instructions: `man apt`
Command to run an audit: `sudo lynis audit system`