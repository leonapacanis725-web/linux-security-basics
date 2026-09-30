# Security Findings

## Finding 1: File Permissions

`README.md` originally had `0644` permissions.

I changed the permissions to `0600` to restrict access to the file owner, then restored the file to `0644`.

**Security takeaway:** Linux file permissions help enforce least privilege by controlling who can read, write, or execute files.

## Finding 2: User and Privilege Analysis

I used the `id` and `groups` commands to examine my Linux account. My account has UID 1000 and GID 1000 and belongs to several groups, including the `sudo` group.

Membership in the `sudo` group means the account can potentially execute permitted commands with elevated privileges.

**Security takeaway:** Privileged group membership should be reviewed because compromised privileged accounts may allow system-level changes. Administrative access should follow the principle of least privilege.

## Finding 3: Root Process Analysis

I used `ps aux` to inspect running processes and their owners.

I initially used `ps aux | grep root`, but this also returned processes containing the word `root` in their command lines, including `rootless`. I then used `ps -U root -u root` to specifically identify processes owned by root.

Observed root-owned processes included `systemd`, `systemd-journald`, `systemd-udevd`, `systemd-logind`, `dhclient`, and `agetty`.

**Security takeaway:** Processes running with elevated privileges should be identified and verified. Simple text searches can produce unrelated matches, so analysts should validate their results.

## Finding 4: Network Service Analysis

I used `ss -tuln` to inspect listening network sockets and identified UDP port 68 listening on `0.0.0.0`.

I then used `sudo ss -tulpn` to identify the process associated with the socket. The output showed UDP port 68 associated with `dhclient` (PID 110). Earlier process enumeration also showed PID 110 running as root.

The evidence was consistent with expected DHCP client network configuration activity.

**Security takeaway:** Listening ports should be correlated with their associated processes and privileges to determine why a service is active and whether its behavior is expected.
