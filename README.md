# LinuxPractice

## 1. Shutdown

Command: `systemctl`

```shell
systemctl poweroff
systemctl shutdown
systemctl reboot
```

### 1.1. Shutdown with Timer

```shell
shutdown -h now
shutdown -h +10 -> shutdown 10 mins with notification
```

To cancel shutdown:
```shell
shutdown -c
```

To shutdown with message:
```shell
shutdown -h 6 "Shutting Down..."
```

To reboot:
```shell
shutdown -r
```

## 2. Sleep Mode

```shell
systemctl suspend
systemctl hibernate
```

## 3. Cron

Reference: [crontab.guru](https://crontab.guru)

`Important Path`:
- /etc/crontab

To edit `Cron`:
```shell
sudo nano /etc/crontab
```
or
```shell
sudo crontab -e
```

To list all active `Cron`:
```shell
sudo crontab -l
```

Note: it will be better if you use `.sh` file to run the cron task. e.g: `* * * * * /home/user/script.sh`.

## 4. PID 1 (Process ID 1)

Everything running on a linux should have a PID, we can check it using:

```shell
ps -ef
```

Most of the time, PID 1 is the `/sbin/init` or `systemd`.

To show all the child process we can use:

```shell
pstree -p
```

For show a process based on their ID is:

```shell
pstree -sp 223
```

## 5. Listing Services and States using systemctl

To list the services:

```shell
systemctl
systemctl list-unit-files --type=service
systemctl list-unit-files --type=service --state=enabled
```

### 5.1. Service Status
You want to know the status of one service or a few specific services:

```shell
systemctl status sshd
systemctl status sudo.service syslog.service sshd.service
```

### 5.1. Starting Service

```shell
sudo systemctl start <service-name>

sudo systemctl start sshd.service mariadb.service firewalld.service
```

### 5.2. Stopping Service

```shell
sudo systemctl stop <service-name>

sudo systemctl stop sshd.service mariadb.service firewalld.service
```

### 5.3. Restart Service

```shell
sudo systemctl restart <service-name>

sudo systemctl restart sshd.service mariadb.service firewalld.service
```

### 5.4. Reload Service

Reload is used for update the service to use new configuration

```shell
sudo systemctl reload <service-name>

sudo systemctl reload sshd.service mariadb.service firewalld.service
```

### 5.5. Enable Service

Enable service means it will automatically start at boot. This does not start the service, so you need to start it manually.

```shell
sudo systemctl enable <service-name>
sudo systemctl start <service-name>
```

### 5.6. Disbale Service

Likewise, disable does not stop the service, thus, we need to stop it manually.

```shell
sudo systemctl disable <service-name>
sudo systemctl stop <service-name>
```

### 5.7. Kill Service

If you want to stop the troublesome service that makes your host hang or something, we can just kill the process.

```shell
sudo systemctl kill <service-name>

# using nuclear options

sudo systemctl kill -9 <service-name>
```

or using PID:

```shell
sudo kill <PID>

# using nuclear options

sudo kill -9 <PID>
```

### `NOTE`: you can check the PID of the status by using `sudo systemctl status <service-name>` or using `top` command.

## 6. Diagnosing Slow Startups

```shell
systemd-analyze blame
```

Sometimes most of the Linux distro bluetooth service is enabled by default, you can turn it off for your server.

## 7. Environment Variable

Reference: [askubuntu](https://askubuntu.com/questions/866161/setting-path-variable-in-etc-environment-vs-profile)

### 7.1. ~/.profile

If you want to add a path (e.g. /your/additional/path) to your PATH variable for your current user only and not for all users of your computer, you normally put it at the end of `~/.profile` like in one of those two examples:

```shell
PATH="/your/additional/path:$PATH"
PATH="$PATH:/your/additional/path"
```

Note that the path priorities are descending from left to right, so the first path has the highest priority. If you add your path on the left of $PATH, it will have the highest priority and executables in that location will override all others. If you add your path on the right, it will have the lowest priority and executables from the other locations will be preferred.

### 7.2. /etc/environment

However, if you need to set that environment variable for all users, I would still not recommend touching `/etc/environment` but creating a file with the file name ending in `.sh` in `/etc/profile.d/`. The `/etc/profile` script and all scripts in `/etc/profile.d` are the global equivalent of each user's personal ~/.profile and executed as regular shell scripts by all shells during their initialization.

## 8. Managing Users and Groups

* `User` home directory belongs in `/home/<user>`
* `User` may belongs to multiple groups
* `User` inside a group have all the privileges of the group
* Human user divided into two categories: `root` or `superuser` and `normal` user
* You can see all users on `/etc/passwd/` and all group in `/etc/group/`

List of all users:
```shell
cat /etc/passwd
```

List of all groups:
```shell
cat /etc/group
```

Passwd command permission:
```shell
ls -l /usr/bin/passwd
```

Some of the commands:

* `useradd` creates new user
* `groupadd` create new group
* `userdel` delete user
* `groupdel` delete group
* `usermod` making changes for existing user
* `passwd` creates and changes password

The main configuration file: `/etc/login.defs`.

### 8.1. Finding UID and GID

```shell
id
# output:
# uid=1000(<user>) gid=1000(<user>) groups=1000(<user>),4(adm),20(dialout),24(cdrom),25(floppy),27(sudo),29(audio),30(dip),44(video),46(plugdev),116(netdev),1001(docker)
```

Adding users and groups in Linux is a fundamental administrative task. Here’s a comprehensive guide on how to add, manage, and understand users and groups in a Linux system.

### Adding a User

1. **Using `useradd` Command**:
   - The `useradd` command creates a new user account.
   - Example:
     ```bash
     sudo useradd username
     ```
   - This command creates a user with the specified `username` and default settings.

2. **Setting Password for the User**:
   - After creating a user, you need to set a password.
   - Example:
     ```bash
     sudo passwd username
     ```
   - You will be prompted to enter and confirm the password.

3. **Creating a Home Directory**:
   - By default, `useradd` might not create a home directory. Use the `-m` option to ensure a home directory is created.
   - Example:
     ```bash
     sudo useradd -m username
     ```

4. **Specifying Shell for the User**:
   - You can specify the default shell for the user using the `-s` option.
   - Example:
     ```bash
     sudo useradd -s /bin/bash username
     ```

5. **Adding User to a Group**:
   - To add a user to a specific group at creation, use the `-g` option for the primary group and `-G` for additional groups.
   - Example:
     ```bash
     sudo useradd -m -s /bin/bash -g primarygroup -G additionalgroup username
     ```

6. **Using `adduser` Command**:
   - `adduser` is a more user-friendly script that uses `useradd` under the hood.
   - Example:
     ```bash
     sudo adduser username
     ```
   - This command prompts you for additional information and creates the home directory by default.

### Adding a Group

1. **Using `groupadd` Command**:
   - The `groupadd` command creates a new group.
   - Example:
     ```bash
     sudo groupadd groupname
     ```

### Managing Users and Groups

1. **Changing User Information**:
   - Use the `usermod` command to modify user information.
   - Example: Add an existing user to an additional group:
     ```bash
     sudo usermod -aG groupname username
     ```

2. **Deleting a User**:
   - Use the `userdel` command to delete a user.
   - Example:
     ```bash
     sudo userdel username
     ```
   - To remove the user's home directory and mail spool, use the `-r` option.
     ```bash
     sudo userdel -r username
     ```

3. **Deleting a Group**:
   - Use the `groupdel` command to delete a group.
   - Example:
     ```bash
     sudo groupdel groupname
     ```

### Viewing User and Group Information

1. **Listing Users**:
   - Users are listed in the `/etc/passwd` file. You can view it using:
     ```bash
     cat /etc/passwd
     ```

2. **Listing Groups**:
   - Groups are listed in the `/etc/group` file. You can view it using:
     ```bash
     cat /etc/group
     ```

3. **Checking Group Membership**:
   - Use the `groups` command to see the groups a user belongs to.
   - Example:
     ```bash
     groups username
     ```

### Example Scenario

#### Adding a New User with Specific Requirements

1. **Create a User with a Home Directory, Specific Shell, and Add to Groups**:
   ```bash
   sudo useradd -m -s /bin/bash -g users -G sudo,developers newuser
   ```

2. **Set Password for the User**:
   ```bash
   sudo passwd newuser
   ```

3. **Verify the User and Group Information**:
   - Check user details:
     ```bash
     cat /etc/passwd | grep newuser
     ```
   - Check group membership:
     ```bash
     groups newuser
     ```

### Summary of Key Commands

| Command                       | Description                                             | Example                                      |
|-------------------------------|---------------------------------------------------------|----------------------------------------------|
| `useradd username`            | Create a new user                                       | `sudo useradd username`                      |
| `passwd username`             | Set password for a user                                 | `sudo passwd username`                       |
| `useradd -m username`         | Create a user with a home directory                     | `sudo useradd -m username`                   |
| `useradd -s /bin/bash username` | Create a user with a specific shell                     | `sudo useradd -s /bin/bash username`         |
| `useradd -g groupname username` | Create a user and assign to a primary group             | `sudo useradd -g groupname username`         |
| `useradd -G group1,group2 username` | Create a user and add to additional groups            | `sudo useradd -G group1,group2 username`     |
| `adduser username`            | User-friendly script to add a new user                  | `sudo adduser username`                      |
| `groupadd groupname`          | Create a new group                                      | `sudo groupadd groupname`                    |
| `usermod -aG groupname username` | Add an existing user to an additional group            | `sudo usermod -aG groupname username`        |
| `userdel username`            | Delete a user                                           | `sudo userdel username`                      |
| `userdel -r username`         | Delete a user and their home directory                  | `sudo userdel -r username`                   |
| `groupdel groupname`          | Delete a group                                          | `sudo groupdel groupname`                    |
| `cat /etc/passwd`             | List all users                                          | `cat /etc/passwd`                            |
| `cat /etc/group`              | List all groups                                         | `cat /etc/group`                             |
| `groups username`             | List groups a user belongs to                           | `groups username`                            |

By understanding and using these commands, you can effectively manage users and groups in a Linux environment, ensuring proper access control and organization within your system.

### 8.2. Creating a Human User with useradd

To create the user and check user:
```shell
sudo useradd user1
id test1
sudo ls -a /home/test1
```

To set password for the user:
```shell
sudo passwd -e test1
```

### 8.3. Adding User to Group

Add `duchess` to `musicians` group:

```shell
sudo usermod -aG musicians duchess
```
















