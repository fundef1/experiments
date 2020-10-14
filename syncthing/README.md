# syncthing

## get tarball 
from ```https://github.com/syncthing/syncthing/releases/``` and unzip the binary\
``` tar -xvf syncthing-linux-amd64-v1.10.0.tar.gz syncthing-linux-amd64-v1.10.0/syncthing``` and move to /usr/bin
``` mv ./syncthing-linux-amd64-v1.10.0/syncthing /usr/bin``` \

## create syncthing group/user
```
addgroup -S syncthing 2>/dev/null
adduser -S -D -H -h /var/lib/syncthing -s /sbin/nologin -G syncthing -g syncthing syncthing 2>/dev/null
```

## /etc/init.d
add options for config and data dirs

## config syncthing
``` syncthing -generate```

