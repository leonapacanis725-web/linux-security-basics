# Security Findings

## Finding 1: File Permissions

README.md originally had 0644 permissions.

I changed the permissions to 0600 to restrict access to the file owner, then restored the file to 0644.

Security takeaway: Linux file permissions help enforce least privilege by controlling who can read, write, or execute files.

## Finding 2: User and Group Membership

My Linux account has UID 1000 and GID 1000.

Using the id and groups commands, I found that the account belongs to several groups, including the sudo group.

Security takeaway: Membership in privileged groups such as sudo should be reviewed because those accounts may be able to perform administrative actions.
## Finding 3: Process Inspection

I used ps aux to inspect running processes and identify which users owned them.

I initially used ps aux | grep root, but this also matched processes containing the word "root" in their command lines, such as "rootless."

I then used ps -u root to filter specifically for processes owned by the root user.

Security takeaway: Reviewing process ownership can help identify programs running with elevated privileges. Search results should be verified because simple text searches can produce unrelated matches.## Finding 4: Network Service Inspection

I used ss -tuln to inspect listening network ports.

The scan showed UDP port 68 listening on 0.0.0.0.

I then used ps aux | grep dhclient and identified the DHCP client process running as root on the eth0 network interface.

Security takeaway: Network ports should be correlated with running processes to understand why a service is listening and whether it is expected.## Finding 4: Network Service Inspection

I used ss -tuln to inspect listening network ports.

The scan showed UDP port 68 listening on 0.0.0.0.

I then used ps aux | grep dhclient and identified the DHCP client process running as root on the eth0 network interface.

Security takeaway: Network ports should be correlated with running processes to understand why a service is listening and whether it is expected.