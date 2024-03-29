# learn-selinux

A list of demonstrations to show SELinux in action.

Sources:
- [Security-Enhanced Linux for mere mortals](https://www.youtube.com/watch?v=_WOKRaM-HI4&t=616s)

## Some simple SELinux commands to check it's status

### Check what policy your system is set to use:

On the server I'm using at the time, the policy is set to permissive ie, not enforcing

#### 1

```bash
cat /etc/selinux/config
```

**Note:** The above is also symlinked to /etc/sysconfig/selinux

Example output:

```bash
# This file controls the state of SELinux on the system.
# SELINUX= can take one of these three values:
#     enforcing - SELinux security policy is enforced.
#     permissive - SELinux prints warnings instead of enforcing.
#     disabled - No SELinux policy is loaded.
SELINUX=permissive
# SELINUXTYPE= can take one of three values:
#     targeted - Targeted processes are protected,
#     minimum - Modification of targeted policy. Only selected processes are protected.
#     mls - Multi Level Security protection.
SELINUXTYPE=targeted
```

#### 2

```bash
/usr/sbin/sestatus
```

Example output:

```bash
SELinux status:                 enabled
SELinuxfs mount:                /sys/fs/selinux
SELinux root directory:         /etc/selinux
Loaded policy name:             targeted
Current mode:                   permissive
Mode from config file:          permissive
Policy MLS status:              enabled
Policy deny_unknown status:     allowed
Max kernel policy version:      31
```

#### 3

```bash
/usr/sbin/getenforce
```

Example output:

`Permissive`

## How Does SELinux Work?

2 concepts involved, 

- Labelling
- Type Enforcement

### Labelling

Files, processes, ports etc are all labelled with an SELinux context. For files and directories, these are stored as extended attributes on the filesystem. For processes, ports etc - the kernel managed these labels.

Labels are in the format of

`user:role:type:level(optional)`

## Example - Simple Apache Server

- httpd (Apache) runs from a binary executable launched from /usr/sbin.
- If you look at that file's SELinux context, you see that its type is `httpd_exec_t`

```bash
ls -lZ /usr/sbin/httpd
```

Output:

> -rwxr-xr-x. root root system_u:object_r:httpd_exec_t:s0 /usr/sbin/httpd

This shows it's labelled with `httpd_exec_t`. 

If we look at the httpd's server configuration directory, we'll see it's labelled with `httpd_config_t`

```bash
ls -dZ /etc/httpd/
```

Output: 

> drwxr-xr-x. root root system_u:object_r:httpd_config_t:s0 /etc/httpd/

And if we look at the log directory, we'll see it's labelled with `httpd_logs_t`

```bash
ls -dZ /var/log/httpd/
```

Output: 

> drwx------. root root system_u:object_r:httpd_log_t:s0 /var/log/httpd

And the content is `httpd_sys_content_t`:

```bash
ls -dZ /var/www/html/
```

Output: 

> drwxr-xr-x. root root system_u:object_r:httpd_sys_content_t:s0 /var/www/html/

The startup script:

```bash
ls -lZ /usr/lib/systemd/system/httpd.service
```

Output:

> -rw-r--r--. root root system_u:object_r:httpd_unit_file_t:s0 /usr/lib/systemd/system/httpd.service

The process running in memory also has an SELinux label associated with it, `httpd_t`:

```bash
ps axZ | grep [h]ttpd
```

Output:

```bash
system_u:system_r:httpd_t:s0     1230 ?        Ss     0:00 /usr/sbin/httpd -DFOREGROUND
system_u:system_r:httpd_t:s0     1231 ?        S      0:00 /usr/sbin/httpd -DFOREGROUND
system_u:system_r:httpd_t:s0     1232 ?        S      0:00 /usr/sbin/httpd -DFOREGROUND
system_u:system_r:httpd_t:s0     1233 ?        S      0:00 /usr/sbin/httpd -DFOREGROUND
system_u:system_r:httpd_t:s0     1234 ?        S      0:00 /usr/sbin/httpd -DFOREGROUND
system_u:system_r:httpd_t:s0     1235 ?        S      0:00 /usr/sbin/httpd -DFOREGROUND
```

