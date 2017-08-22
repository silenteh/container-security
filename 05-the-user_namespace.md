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



