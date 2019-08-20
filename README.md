# learn-selinux

A list of demonstrations to show SELinux in action.

## Some simple SELinux commands to check it's status

1. Check what policy your system is set to use:

```bash
cat /etc/selinux/config
```

**Note:** The above is also symlinked to /etc/sysconfig/selinux

Example output:

> # This file controls the state of SELinux on the system.
> # SELINUX= can take one of these three values:
> #     enforcing - SELinux security policy is enforced.
> #     permissive - SELinux prints warnings instead of enforcing.
> #     disabled - No SELinux policy is loaded.
> SELINUX=permissive
> # SELINUXTYPE= can take one of three values:
> #     targeted - Targeted processes are protected,
> #     minimum - Modification of targeted policy. Only selected processes are protected.
> #     mls - Multi Level Security protection.
> SELINUXTYPE=targeted

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

```bash
/usr/sbin/getenforce
```

Example output:

> Permissive
