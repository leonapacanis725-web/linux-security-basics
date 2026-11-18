# Linux Security Basics

## 1. Objective

Review a Linux learning environment to understand how file permissions, account privileges, running processes, and listening network sockets affect system security. The investigation focuses on collecting command-line evidence, validating results, and identifying practical least-privilege considerations.

## 2. Environment & Tools

- **Environment:** Linux terminal / Bash
- **Tools:** `stat`, `chmod`, `id`, `groups`, `ps`, `ss`, `grep`, and `sudo`
- **Version control:** Git and GitHub
- **Supporting notes:** `commands.md` and `findings.md`

This was performed in my own Linux learning environment, not on a production system.

## 3. Methodology & Commands

**Inspect and modify a test file's permissions:**

```bash
stat README.md
chmod 600 README.md
stat README.md
chmod 644 README.md
stat README.md
```

The file began with mode `0644`, was temporarily changed to `0600`, and was then restored to `0644`. This demonstrated how owner-only access differs from read access for group and other users.

**Review the current account and group membership:**

```bash
id
groups
```

These commands display the account's UID/GID and its group memberships, including whether it belongs to a privileged group.

**Inspect running processes and validate process ownership:**

```bash
ps aux
ps aux | grep root
ps -U root -u root
```

The initial text search was broad and returned command lines containing the word `root`, including `rootless`. The ownership-specific command was used to narrow the results to processes running as root.

**Inspect listening network sockets and identify the associated process:**

```bash
ss -tuln
sudo ss -tulpn
```

The first command lists listening TCP/UDP sockets. The second adds process details so a listening socket can be correlated with the process using it.

## 4. Key Findings

- **File permissions:** `README.md` originally had mode `0644`; it was changed to `0600` for the test and restored to `0644`.
- **Account privileges:** The inspected account had UID 1000 and GID 1000 and belonged to the `sudo` group, indicating it could request permitted elevated operations.
- **Process ownership:** Root-owned processes observed included `systemd`, `systemd-journald`, `systemd-udevd`, `systemd-logind`, `dhclient`, and `agetty`.
- **Search validation:** `ps aux | grep root` produced unrelated text matches such as `rootless`; filtering by process owner provided a more accurate check.
- **Network socket:** UDP port 68 was listening on `0.0.0.0`. Process inspection associated it with `dhclient` (PID 110), which was also observed running as root. The evidence was consistent with expected DHCP client activity in this environment.

## 5. Remediation / Security Takeaway

Use restrictive permissions for files containing sensitive information and verify permission changes after applying them. Review privileged group membership and root-owned processes regularly, granting administrative access only when needed. When investigating network exposure, correlate listening ports with their owning processes and validate findings rather than relying on broad text searches alone.

## Project Files

- `commands.md` — permission test details and commands
- `findings.md` — detailed observations and security takeaways

## Scope & Disclaimer

This is a hands-on educational review of my own Linux learning environment. Findings describe the state observed during the exercise and should not be interpreted as a security assessment of a production system.

## Continue Learning with Better Hacker

Explore the related free beginner lesson and hands-on practice:

[Linux Security lesson](https://betterhacker.org/#linux-lesson) · [Defensive Security practice](https://betterhacker.org/#practice-defensive-security)
