# Create a Custom Apache User (`ammar`)

**Date:** 3 November 2026  
**Platform:** KodeKloud — xFusionCorp Industries, Stratos Datacenter  
**Target Server:** App Server 3 (`stapp03`)  
**Category:** Linux User Administration

## Objective

My objective was to create a dedicated Linux user named `ammar` on App Server 3. The account required the unique UID `1528` and the home-directory path `/var/www/ammar`.

## Key Concepts

- **Service isolation:** Linux user accounts provide identities that the operating system uses to manage access to files and processes. A dedicated account can help keep an application's files and processes separate from those belonging to other accounts.
- **UIDs:** A user's UID is the numeric identity used by Linux for ownership and access checks. The username is the human-readable name associated with that UID. A UID should be unique to avoid account-identity conflicts.
- **Home directories:** The home-directory field in the account database records the path associated with a user. Specifying that path and creating the directory on the filesystem are related but distinct operations.

## Execution & Terminal Output

### 1. Connect to App Server 3

From the jump host, I connected to `stapp03` and checked the hostname:

```bash
ssh banner@stapp03
hostname
```

Output:

```text
stapp03
```

### 2. Check for a UID conflict

Before creating the account, I checked whether UID `1528` was already present:

```bash
getent passwd 1528
```

The command returned no output, so I did not find an existing account with that UID in the account database.

### 3. Create the user

I ran the following command:

```bash
sudo useradd -u 1528 -d /var/www/ammar -m ammar
```

The command returned to the shell prompt without displaying an error.

The options I used were:

- `-u 1528`: assigns UID `1528`.
- `-d /var/www/ammar`: sets the account's home-directory path.
- `-m`: requests creation of the home directory.
- `ammar`: specifies the username.

## Verification

I queried the account database again to check the new account:

```bash
getent passwd 1528
```

Output:

```text
ammar:x:1528:1528::/var/www/ammar:/bin/bash
```

| Field | Recorded value |
|---|---|
| Username | `ammar` |
| UID | `1528` |
| Primary GID | `1528` |
| Home directory | `/var/www/ammar` |
| Login shell | `/bin/bash` |

This output confirms that the account database contains the requested username, UID, and home-directory path.

## Lessons Learned & Notes

- **Checking UIDs first:** The initial `getent passwd 1528` query returned no output. Checking first helped me avoid assigning a UID that was already listed in the account database.
- **A missed verification step:** I learned that seeing the home-directory path in the account database does not by itself prove that the directory exists on the filesystem. I used the `-m` option to request its creation, but I forgot to run `ls -ld /var/www/ammar` to check the directory directly.
- **Improving my verification routine:** In a future run, I will check the account with `id ammar` and inspect the home directory with `ls -ld /var/www/ammar`. These checks provide additional evidence beyond the passwd database entry.

## Skills Practiced / Key Takeaways

- Connecting to a remote Linux server over SSH.
- Confirming the target host with `hostname`.
- Checking for an existing UID with `getent`.
- Creating a Linux user with a specified UID and home-directory path.
- Requesting home-directory creation with `useradd -m`.
- Reading account information from the passwd database.

## Task Completion Evidence

![KodeKloud task completion](images/01-create-apache-user.png)
