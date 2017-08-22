### The user namespace

Let's see what the user namesoace can do for us  

```
$ addgroup --gid 5000 dockerns

$ adduser --home /home/dockerns --shell /bin/false --disabled-password --gecos "" --uid 5000 --gid 5000 dockerns

Create a new file at: /etc/docker/daemon.json and add this entry:

{
"userns-remap": "dockerns:dockerns"
}
```

Restart now the docker daemon and check few things:

- Run: `docker images` what is the output ? Why ?
- Check these files: `/etc/subuid` and `/etc/subgid`
- Check this folder: `/var/lib/docker`

#### Limitations

There are known limitations for now when going with the user namespace, specifically:

* sharing PID or NET namespaces with the host (--pid=host or --network=host)
* --read-only container filesystem. This is a Linux kernel restriction against remounting an already-mounted filesystem with modified flags when inside a user namespace.
* external volume drivers which are unaware or incapable of using daemon user mappings.

If you can live with these limitations it's agood addition to your defence in depth strategy.
