### The USER directive

Create a new ubuntu image which by default switches to an unprivileged user.  
Now run it with: `docker run -it IMAGE_NAME`  

Try now to `remount` or to install `build-essential`  

#### Not all roses...

You can still fork-bomb the server though....try it yourself via:

```
```

So what can we do about it ?  

#### Unprivileged user and Docker ulimit

Try now to run your image with the unprivileged user with the Docker ulimit like this

```
docker run -it --ulimit nofile=1024:1024 --ulimit nproc=1024:1024 --rm IMAGE_NAME
```

Now try to fork bomb.  
Try then to change the ulimit (Ex ulimit -u 1024)

Now that's better! You can set any ulimit in this way.  
To see the current values, inspect now the container.
