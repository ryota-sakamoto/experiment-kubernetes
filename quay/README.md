quay
===

[quay](https://github.com/quay/quay)はDocker Registryである  
最近OSSになった

[Deploy Red Hat Quay - Basic](https://access.redhat.com/documentation/en-us/red_hat_quay/3/html/deploy_red_hat_quay_-_basic/index)にデプロイする手順が乗っていたことに気づいたので、`docker-compose`で試す  
以下はメモ

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

1. アクセスするとDBの情報を入力することになる、localhostは不可
2. 進めると下記のエラー発生、これはDBの文字コードを`utfmd4`にしていたから`latin1`にしたら通った

![](image.png)

3. DBのマイグレーション完了後画面
![image](https://user-images.githubusercontent.com/18019529/69979616-8c006c80-1526-11ea-8322-601c26c7e726.png)

4. Redisが必要
![image](https://user-images.githubusercontent.com/18019529/69979910-18129400-1527-11ea-8097-ea58b0e1b173.png)

5. メモ
![image](https://user-images.githubusercontent.com/18019529/69979958-2eb8eb00-1527-11ea-802f-36fdaf6d0538.png)
![image](https://user-images.githubusercontent.com/18019529/69980017-4abc8c80-1527-11ea-80d6-edf8b41fec84.png)

6. config後に下記コマンド実行するといけるはずだけどいけない

何もconfigを作らずに動かすとエラー出る

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
