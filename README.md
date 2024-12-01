# linux-ssh-logging-guide
This guide walks through setting up SSH between two machines on the same network, logging into another system, and managing logs.

## Index
[1. Simple SSH](#1simple-ssh)

[2. SSH Key-Based Authentication](#2ssh-key-based-authentication)

[3. Allow/Deny Users for SSH](#3allowdeny-users-for-ssh)

[4. Log Management](#4log-management)
 
  - [Access System Logs](#access-system-logs)
  - [Custom Log Configuration](#5custom-log-configuration)

---

# Steps for SSH Setup Between Two Machines

## 1.Simple SSH

- **Checking Network Connectivity**

Ensure both machines are on the same network and get their IP addresses:

**On Machine A**:

`ifconfig`  Or `ip a`

![Screenshot from 2024-12-01 21-52-49](https://github.com/user-attachments/assets/190038f8-0b54-465e-aaa6-3a00d7fcd46e)


**On Machine B**:

![Screenshot from 2024-12-01 21-54-37](https://github.com/user-attachments/assets/b061de60-7571-4e5b-8f8d-2892cb749919)

- **Testing Ping**

Check connectivity between machines:

![Screenshot from 2024-12-01 21-56-07](https://github.com/user-attachments/assets/1cc04ef6-8d6e-4091-a4d7-41dbe7dc99da)


- **Login to Machine B**
   
a) Verify the user is available:

![Screenshot from 2024-12-01 21-58-08](https://github.com/user-attachments/assets/2c992242-3e2c-4bec-ba22-2061a71abe9b)

![Screenshot from 2024-12-01 21-58-23](https://github.com/user-attachments/assets/67c0148d-67ec-4af9-a87f-57d08c62792b)

b) Log in as the user ritik from Machine A:

![Screenshot from 2024-12-01 23-59-09](https://github.com/user-attachments/assets/09f846d9-baec-484d-9185-6dc47aa3a17c)

c) On Machine B, verify the directory and file:

Create a file as user ritik through Machine A:

![Screenshot from 2024-12-01 22-04-08](https://github.com/user-attachments/assets/28d38a61-2cdb-4c09-aa8b-518285085140)

Verify on Machine B: 

![Screenshot from 2024-12-01 22-05-55](https://github.com/user-attachments/assets/a20bba99-e3e3-4167-9a51-07615cc1de9f)

---

- **Login as Root**
  
Log in as the root user on Machine B:

![Screenshot from 2024-12-01 22-25-09](https://github.com/user-attachments/assets/cf91a99e-7d1f-4190-8c3e-cd0f55fb0faf)


## 2.SSH Key-Based Authentication

- **Generate SSH Key on Machine A**:

![Screenshot from 2024-12-01 22-30-56](https://github.com/user-attachments/assets/8b92a5e4-8a16-43d2-a697-e04a5253534b)

Files: id_rsa (private key) and id_rsa.pub (public key)
 
- **Copy Public Key to Machine B**:

![Screenshot from 2024-12-01 22-33-25](https://github.com/user-attachments/assets/b0105396-e780-4abe-9433-102b91872ad6)

Enter the password when prompted.

- **Verify Password-less Login**:

![Screenshot from 2024-12-01 22-34-54](https://github.com/user-attachments/assets/d08fa61f-8be7-4954-a8d8-3073e289212a)


## 3.Allow/Deny Users for SSH

- **Edit SSH configuration on Machine B**:

vim /etc/ssh/sshd_config

- **Add the following lines under Authentication**:

AllowUsers ritik root

DenyUsers  harry

![Screenshot from 2024-12-01 22-40-07](https://github.com/user-attachments/assets/2fade212-7da5-4f54-9e2b-58d99931095d)

- **Restart the SSH service**:

![Screenshot from 2024-12-01 22-41-59](https://github.com/user-attachments/assets/7b09841f-37a0-4555-816a-cd193846d25f)

- **Test logging in with different users**:

![Screenshot from 2024-12-01 22-49-34](https://github.com/user-attachments/assets/e93d2f23-e495-42a1-a1cf-99a6d9711690)

ssh harry@192.168.1.13  # Should fail

![Screenshot from 2024-12-01 22-48-59](https://github.com/user-attachments/assets/ecd4a786-3482-4e45-881b-574905b4a56a)

ssh ritik@192.168.1.13   # Should succeed

## 4.Log Management
### Access System Logs
- **A. View logs**:
 
![Screenshot from 2024-12-01 23-16-22](https://github.com/user-attachments/assets/385863fd-c22d-459f-9b44-4c3bfba1893b)

**Explain**:

**System Logs**:
  - boot.log: Contains logs related to the system boot process.
    
  - messages: General system messages that may contain errors, warnings, or general system activity.
    
  - secure: Contains authentication and authorization logs, such as SSH login attempts, sudo, and other security-related events.

  - cron: Logs related to cron jobs, which are scheduled tasks on the system.
  
  - lastlog: Contains information about the last login times for all users.
  
  - wtmp: A binary file that logs all logins and logouts on the system.
  
  - btmp: Logs failed login attempts.
  
  - audit: Contains audit logs, which track various security-relevant events on the system.

**VMware Logs**:
    
  - vmware-network.log, vmware-vmsvc-root.log, etc.: Logs related to VMware virtual machines and services. These files might be used for diagnosing issues with VMware tools or networking.

**Application-Specific Logs**:
   
  - dmesg: Kernel logs related to system hardware and driver issues.

  - firewalld: Logs related to the firewall service.
  
  - sssd: Logs for the System Security Services Daemon, which provides authentication services.
  
  - rhsm: Red Hat Subscription Manager logs.
   
  - insights-client: Logs from the Red Hat Insights client, which provides system diagnostics.

**Other Logs**:
    
  - tuned: Logs related to the tuning of system settings for performance.
  
  - cups: Logs from the Common Unix Printing System, related to printer management.
  
  - xferlog: Logs related to FTP transfers.

  - samba: Logs related to the Samba file-sharing service.

**Older Log Files**:
    
  - messages-20241124, secure-20241124: These are archived log files, probably for previous dates. They can be helpful for reviewing past events.

---

- **B. Use journalctl for logs**:

  journalctl is a command-line utility used to query and display log entries from the systemd journal. This tool is part of the systemd suite and is used to view logs recorded by system services, the kernel, and other system components in a centralized manner.

![Screenshot from 2024-12-01 23-28-17](https://github.com/user-attachments/assets/0fda6a9e-1771-4bc5-a6b5-4f63ce1ef18b)

   a) View the last 5 logs:
    
![Screenshot from 2024-12-01 23-28-56](https://github.com/user-attachments/assets/ed6a0e83-56c7-45f3-960b-4463f0b997e6)

   b) View detailed logs:

**journalctl -xe** 

shows the most recent log entries and adds extra details, making it easier to troubleshoot errors, warnings, or system issues.

![Screenshot from 2024-12-01 23-30-36](https://github.com/user-attachments/assets/45287568-d33c-4f1b-8af3-fd9a97dde6fd)


   c) View logs with specific priority:

  -Error logs

  journalctl -p err or journalctl -p 3

 ![Screenshot from 2024-12-01 23-34-16](https://github.com/user-attachments/assets/1e7c8710-cee3-472c-9e80-330f530406d3)

 Check more:
       
![Screenshot from 2024-12-01 23-36-51](https://github.com/user-attachments/assets/89da2a3a-d9b8-41dd-a72d-b36bcc73a015)

---

**C. Filter logs by PID or service**:

  - **by PID**

![Screenshot from 2024-12-01 23-38-44](https://github.com/user-attachments/assets/31cd4148-f3f4-44e7-8669-af5a52d74740)


  - **by service**

![Screenshot from 2024-12-01 23-39-49](https://github.com/user-attachments/assets/ba414808-f603-4c77-a135-536aec0db2f2)

## 5.Custom Log Configuration
- **Create a custom log file**:

![Screenshot from 2024-12-01 23-48-36](https://github.com/user-attachments/assets/ae57e6c5-54c6-4e6d-9ae9-a44eeee98e02)


- **Configure rsyslog**:

![Screenshot from 2024-12-01 23-51-26](https://github.com/user-attachments/assets/800e02c5-c40a-4dc7-a609-01f60348fcc7)

Add:

*.debug /var/log/MY_log

![Screenshot from 2024-12-01 23-50-29](https://github.com/user-attachments/assets/e39379e8-814e-4d19-b0ad-3d89cb6ec0b5)


- **Restart the rsyslog service**:

systemctl restart rsyslog.service

- **Test by adding logs**:
  
![Screenshot from 2024-12-01 23-54-52](https://github.com/user-attachments/assets/515793ea-2878-4e68-9f2f-02e483be461e)

The logger command in Linux is used to write custom messages to the system log (typically managed by rsyslog or journalctl). The command interacts with the syslog system, allowing users or scripts to log messages for troubleshooting, auditing, or informational purposes.

![Screenshot from 2024-12-01 23-55-55](https://github.com/user-attachments/assets/deccbb98-924f-42ac-b939-309ac47fe5b8)
