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

Security takeaway: Reviewing process ownership can help identify programs running with elevated privileges. Search results should be verified because simple text searches can produce unrelated matches.

## Finding 4: Network Service Inspection

I used ss -tuln to inspect listening network ports.

The scan showed UDP port 68 listening on 0.0.0.0.

I then used ps aux | grep dhclient and identified the DHCP client process running as root on the eth0 network interface.

Security takeaway: Network ports should be correlated with running processes to understand why a service is listening and whether it is expected.

## User and Privilege Analysis

I used the `id` command to examine my user account and group memberships. My account belongs to the `sudo` group, which means it can potentially execute commands with elevated privileges. This demonstrates why privileged accounts should follow the principle of least privilege.

## Root Process Analysis

I examined processes running with root privileges. I learned that searching with `grep root` can produce false or unrelated matches because it searches the entire command text. Using `ps -U root -u root` provided a cleaner list of processes actually owned by root.

The root-owned processes observed included system services such as `systemd`, `systemd-journald`, `systemd-udevd`, `systemd-logind`, `dhclient`, and `agetty`.

## Network Service Analysis

I used `ss -tuln` to inspect listening network sockets and found UDP port 68. I then used `sudo ss -tulpn` to identify the associated process.

The output showed that UDP port 68 was associated with `dhclient` (PID 110). Earlier process enumeration also showed that PID 110 was running as root. This demonstrated how an analyst can correlate network activity with running processes.

Based on the evidence examined, the DHCP client appeared consistent with expected network configuration activity.
