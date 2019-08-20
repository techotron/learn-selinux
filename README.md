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

> &#8203;# This file controls the state of SELinux on the system.
> &#8203;# SELINUX= can take one of these three values:
> &#8203;#     enforcing - SELinux security policy is enforced.
> &#8203;#     permissive - SELinux prints warnings instead of enforcing.
> &#8203;#     disabled - No SELinux policy is loaded.
> SELINUX=permissive
> &#8203;# SELINUXTYPE= can take one of three values:
> &#8203;#     targeted - Targeted processes are protected,
> &#8203;#     minimum - Modification of targeted policy. Only selected processes are protected.
> &#8203;#     mls - Multi Level Security protection.
> SELINUXTYPE=targeted

#### 2

```bash
/usr/sbin/sestatus
```

Example output:

> SELinux status:                 enabled
> SELinuxfs mount:                /sys/fs/selinux
> SELinux root directory:         /etc/selinux
> Loaded policy name:             targeted
> Current mode:                   permissive
> Mode from config file:          permissive
> Policy MLS status:              enabled
> Policy deny_unknown status:     allowed
> Max kernel policy version:      31

#### 3

```bash
/usr/sbin/getenforce
```

Example output:

> Permissive

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


