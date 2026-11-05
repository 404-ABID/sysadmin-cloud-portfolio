# Create a User with a Non-Interactive Shell

**Date:** 5 November 2026  
**Platform:** KodeKloud — xFusionCorp Industries, Stratos Datacenter  
**Target Servers:** App Server 1 (`stapp01`), App Server 2 (`stapp02`)  
**Category:** Linux User Administration

## Objective

I worked through the KodeKloud task pattern of creating a local user account with a non-interactive login shell. Across the three recorded attempts, I created `mark` on App Server 1, `kirsty` on App Server 2, and `kareem` on App Server 2; the final screenshot confirms completion of the `kareem` challenge.

## Execution & Terminal Output

### 1. Connect and check the target host

For the first attempt, I connected to App Server 1 as `tony` and confirmed the host before making account changes.

```bash
ssh tony@stapp01
hostname
```

```text
stapp01
```

For the second and third attempts, I connected to App Server 2 as `steve` and confirmed the host.

```bash
ssh steve@stapp02
hostname
```

```text
stapp02
```

### 2. Attempt 1 — create `mark` on App Server 1

I checked for `mark`, then attempted to create the account without administrative privileges.

```bash
id mark
useradd mark
```

```text
id: ‘mark’: no such user
useradd: Permission denied.
useradd: cannot lock /etc/passwd; try again later.
```

I corrected the permission issue with `sudo`, but did not specify the required shell.

```bash
sudo useradd mark
id mark
```

```text
uid=1001(mark) gid=1001(mark) groups=1001(mark)
```

The automated grader failed this attempt because I did not assign a non-interactive shell. The account existed, but it was using the system's default shell."

### 3. Attempt 2 — create `kirsty` on App Server 2

I checked for `kirsty` and created the account with `/sbin/nologin`.

```bash
id kirsty
sudo useradd -s /sbin/nologin kirsty
id kirsty
getent passwd kirsty
```

```text
id: ‘kirsty’: no such user
uid=1001(kirsty) gid=1001(kirsty) groups=1001(kirsty)
kirsty:x:1001:1001::/home/kirsty:/sbin/nologin
```

Despite the account appearing in both verification outputs, KodeKloud reported:

```text
user 'kirsty' does not exist on App Server 2
```

The automated grader failed the task, claiming the user was missing. Since my terminal checks proved the account was correctly set up, I logged this as a platform glitch rather than a misconfiguration.
### 4. Attempt 3 — create `kareem` on App Server 2

For the third attempt, I again confirmed that the target account did not already exist.

```bash
id kareem
```

```text
id: ‘kareem’: no such user
```

I created the account and specified the non-interactive shell in the same command.

```bash
sudo useradd -s /sbin/nologin kareem
```

I then checked the account identity, the passwd database result, and the local `/etc/passwd` entry.

```bash
id kareem
getent passwd kareem
sudo grep kareem /etc/passwd
```

```text
uid=1001(kareem) gid=1001(kareem) groups=1001(kareem)
kareem:x:1001:1001::/home/kareem:/sbin/nologin
kareem:x:1001:1001::/home/kareem:/sbin/nologin
```

The `-s` option assigns `/sbin/nologin` as the account's shell during creation. The final passwd-field checks show that the configured shell is `/sbin/nologin`.

## Verification

I used `id` to confirm that each created account had a UID and primary group. For the second and third attempts, I also inspected the passwd entry to check the configured shell.

| Attempt | Server | User | What the checks showed | Result |
|---|---|---|---|---|
| 1 | `stapp01` | `mark` | User was created, but the required shell was not set | Failed |
| 2 | `stapp02` | `kirsty` | Terminal confirmed user existence and `/sbin/nologin`, but the platform validator reported the user as missing. | Failed |
| 3 | `stapp02` | `kareem` | Account and `/sbin/nologin` shell were shown in the checks | Completed |

## Lessons Learned & Notes

- **Use `sudo` for account changes:** The first `useradd` command failed because it was run without administrator privileges.
- **Set the requested shell:** Creating a user alone is not enough when a specific shell is required. Use `-s /sbin/nologin` when creating the account.
- **Check more than the username:** `id` shows the user ID and groups, while `getent passwd` shows the account's login shell.
- **Platform glitch:** During the `kirsty` attempt, the automated validator claimed the user did not exist, but my terminal checks (`getent passwd`) proved the account was correctly configured. Since my local outputs were correct, I treated this as a platform glitch rather than a configuration error.

## Skills Practiced

- Creating local Linux user accounts with `useradd` and administrative privileges through `sudo`.
- Assigning a non-interactive shell with `useradd -s /sbin/nologin`.
- Verifying account identity and groups with `id`.
- Inspecting account records with `getent passwd` and `/etc/passwd`.

## Terminal Session

I have included my actual terminal session in a separate log file. It contains the exact commands I ran and the outputs from the task.

[View my actual terminal session](logs/03-create-user-non-interactive-shell.log)

## Task Completion Evidence

**Attempt 1: Failed (Missing non-interactive shell)**
![KodeKloud task failed - Attempt 1](image/03-create-user-non-interactive-shell-attempt-1.png)

**Attempt 2: Failed (Platform validator glitch)**
![KodeKloud task failed - Attempt 2](image/03-create-user-non-interactive-shell-attempt-2.png)

**Attempt 3: Passed (Successful configuration)**
![KodeKloud task passed - Attempt 3](image/03-create-user-non-interactive-shell-attempt-3.png)
---

