# Archiving and Logging Data
## Create, Extract, Compress, and Manage tar Backup Archives
Command to **extract** the TarDocs.tar archive to the current directory: (stackoverflow)
`tar xvvf TarDocs.tar --strip-components 0`

Command to **create** the Javaless_Doc.tar archive from the TarDocs/ directory, while excluding the TarDocs/Documents/Java directory: [tar manual](https://www.gnu.org/software/tar/manual/tar.html)
`tar cvvWf Javaless_Doc.tar --exclude=Java ./TarDocs/Documents`
Using absolute path: `tar cvvWf Javaless_Doc.tar --exclude=Java /home/sysadmin/Projects/TarDocs/Documents`

Command to ensure Java/ is not in the new Javaless_Docs.tar archive:
`tar tvvf Javaless_Docs.tar | grep -i java`

Command to create an incremental archive called logs_backup_tar.gz with only changed files to snapshot.file for the /var/log directory:
`sudo tar cvvWf logs_backup_tar.gz --listed-incremental=snapshot.snar var/log`

## Create, Manage, and Automate Cron Jobs
Cron job for backing up the /var/log/auth.log file:
`0 6 * * 3 tar czf /auth_backup.tgz /var/log/auth.log`

##  Write Basic Bash Scripts
Brace expansion command to create the four subdirectories:
`sudo mkdir -p ~/backups/{freemem,diskuse,openlist,freedisk}`

Paste your system.sh script edits below:
```bash
#!/bin/bash
# Free memory output to a free_mem.txt file
free -h > ~/backups/freemem/free_mem.txt
# Disk usage output to a disk_usage.txt file
du -h > ~/backups/diskuse/disk_usage.txt
# List open files to a open_list.txt file
lsof > ~/backups/openlist/open_list.txt
# Free disk space to a free_disk.txt file
df -h > ~/backups/freedisk/free_disk.txt
```

Command to make the system.sh script executable: `chmod +x system.sh`

Commands to test the script and confirm its execution:
`sudo ./system.sh`
`cat ~/backups/diskuse/disk_usage.txt`
`cat ~/backups/freedisk/free_disk.txt`
`cat ~/backups/freemem/free_mem.txt`
`cat ~/backups/openlist/open_list.txt`

Command to copy system to system-wide cron directory: 
`sudo cp system.sh /etc/cron.weekly`

## Manage Log File Sizes
Configure a log rotation scheme that backs up authentication messages to the /var/log/auth.log.
```bash
/var/log/auth.log {
   weekly
   rotate 49
   notifempty
   delaycompress
   missingok
   endscript
}
```

## Check for Policy and File Violations
Command to verify auditd is active: `systemctl status auditd`

Command to set number of retained logs and maximum log file size:
```bash
max_log_file = 35
num_logs = 7
```

Command using auditd to set rules for /etc/shadow, /etc/passwd and /var/log/auth.log:
`-w /etc/shadow -p rwa -k hashpass_audit`
`-w /etc/passwd -p rwa -k userpass_audit`
`-w /cat/log/auth.log -p rwa -k authlog_audit`

Command to restart auditd: `sudo systemctl restart auditd`

Command to list all auditd rules: `sudo auditctl -l`

Command to produce an audit report: `sudo aureport -au`

Create a user with sudo useradd attacker and produce an audit report that lists account modifications: `sudo aureport -m`
```bash
Account Modifications Report
=================================================
# date time auid addr term exe acct success event
=================================================
1. 04/18/2021 01:37:02 1000 UbuntuDesktop pts/0 /usr/sbin/useradd attacker yes 1605693
2. 04/18/2021 01:37:02 1000 UbuntuDesktop pts/0 /usr/sbin/useradd ? yes 1605697
```
Command to use auditd to watch /var/log/cron: `sudo auditctl -w /var/log/cron`

Command to verify auditd rules: `sudo auditctl -l`

## Perform Various Log Filtering Techniques
Command to return journalctl messages with priorities from emergency to error:
`sudo journalctl -p emerg..err`

Command to check the disk usage of the system journal unit since the most recent boot:
sudo journalctl --disk-usage

Command to remove all archived journal files except the most recent two:
`sudo journalctl --vacuum-files=2`

Command to filter all log messages with priority levels between zero and two, and save output to /home/sysadmin/Priority_High.txt:
`sudo journalctl -p 0..2 > ~/Priority_High.txt`

Command to automate the last command in a daily cronjob. Add the edits made to the crontab file below:
`0 23 * * * tar czf /auth_backup.tgz /var/log/auth.log`