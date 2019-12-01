quay
===

[quay](https://github.com/quay/quay)はDocker Registryである  
最近OSSになった

## memo

`$QUAY_DEVEL_HOME`は`quay`をcloneしたディレクトリ

```
$ docker images
REPOSITORY            TAG                 IMAGE ID            CREATED              SIZE
quay                  devel               8b238bb44e43        About a minute ago   1.91GB
```

下記のコマンドで`https://localhost:8443/`にアクセス可能  
BASICは`quayconfig`/`password`

```
$ docker run -it --rm --name quay \
      -v $QUAY_DEVEL_HOME/quay-config:/conf/stack \
      -v $QUAY_DEVEL_HOME/quay-storage:/datastorage \
      -v $QUAY_DEVEL_HOME/quay:$QUAY_DEVEL_HOME/quay \
      -p 8080:8080 \
      -p 8443:8443 \
      -p 9092:9092 \
      -e QUAY_DEVEL_HOME=$QUAY_DEVEL_HOME \
      -e ENCRYPTED_ROBOT_TOKEN_MIGRATION_PHASE=new-installation quay:devel config password
```

```
$ docker run --rm --name quay \
      -v $QUAY_DEVEL_HOME/quay-config:/conf/stack \
      -v $QUAY_DEVEL_HOME/quay-storage:/datastorage \
      -v $QUAY_DEVEL_HOME/quay:$QUAY_DEVEL_HOME/quay \
      -p 8080:8080 \
      -p 8443:8443 \
      -p 9092:9092 \
      -e QUAY_DEVEL_HOME=$QUAY_DEVEL_HOME \
      -e ENCRYPTED_ROBOT_TOKEN_MIGRATION_PHASE=new-installation quay:devel
   __   __
  /  \ /  \     ______   _    _     __   __   __
 / /\ / /\ \   /  __  \ | |  | |   /  \  \ \ / /
/ /  / /  \ \  | |  | | | |  | |  / /\ \  \   /
\ \  \ \  / /  | |__| | | |__| | / ____ \  | |
 \ \/ \ \/ /   \_  ___/  \____/ /_/    \_\ |_|
  \__/ \__/      \ \__
                  \___\ by Red Hat

 Build, Store, and Distribute your Containers


Running all default registry services
Running init script '/quay-registry/conf/init/02_get_kube_certs.sh'
Running init script '/quay-registry/conf/init/certs_create.sh'
Generating a 4096 bit RSA private key
.......................................................................................................................++
.....................++
writing new private key to 'mitm-key.pem'
-----
Running init script '/quay-registry/conf/init/certs_install.sh'
Running init script '/quay-registry/conf/init/copy_config_files.sh'
Running init script '/quay-registry/conf/init/nginx_conf_create.sh'
Running init script '/quay-registry/conf/init/runmigration.sh'
fatal: Not a git repository (or any of the parent directories): .git
fatal: Not a git repository (or any of the parent directories): .git
Skipping Sqlite migration!
Running init script '/quay-registry/conf/init/supervisord_conf_create.sh'
Running init script '/quay-registry/conf/init/zz_boot.sh'
fatal: Not a git repository (or any of the parent directories): .git
Traceback (most recent call last):
  File "./boot.py", line 131, in <module>
    main()
  File "./boot.py", line 120, in main
    raise Exception('Your configuration bundle is either not mounted or setup has not been completed')
Exception: Your configuration bundle is either not mounted or setup has not been completed
```

`shell`モード?にすると

```
[root@62699ccf32cb quay-registry]# ps aux
USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root         1  0.0  0.0   4364  1468 pts/0    Ss   18:04   0:00 /usr/bin/scl enable python27 rh-nginx112 /bin/bash
root         8  0.0  0.1  15128  3064 pts/0    S    18:04   0:00 /bin/bash /var/tmp/sclUAeRJu
root        16  0.0  0.1  15272  3512 pts/0    S    18:04   0:00 /bin/bash
root        26  0.0  0.1  55192  3816 pts/0    R+   18:05   0:00 ps aux
```