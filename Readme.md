# SSH servers for TTSSH evaluation

TTSSH, an SSH extension of Tera Term supports SSH1 and SSH2. SSH1 protocol and some cipher algorithms are not supported by current OpenSSH.

These docker images can build SSH servers for evaluate followings:

- SSH1 protocol
- 56bit DES on SSH1
- camellia128/192/256-cbc/ctr
- blowfish-ctr
- cast128-ctr
- 3des-ctr


## Docker images

- openssh
  - port:8022, user:ttssh2, pass:ttssh2
  - for SSH1 (DES, 3DES, Blowfish), SSH2(camellia128-cbc, camellia192-cbc, camellia256-cbc, camellia128-ctr, camellia192-ctr, camellia256-ctr)
  - OpenSSH + camellia patch + enable 56bit DES (SSH1)
  - Cent OS 6, OpenSSH 5.3p1, OpenSSL 1.0.1e
- twisted
  - port:5022, user:ttssh2, pass:ttssh2
  - for SSH2 (blowfish-ctr, cast128-ctr, 3des-ctr)
  - Twisted Conch sshsimpleserver.py
  - Cent OS 6, python-twisted-conch-8.0.2, python-crypto-2.0.1, python-2.6.6
- dropbear
  - port:9022, user:ttssh2, pass:ttssh2
  - for SSH2 (3des-ctr)
  - Dropbear
  - Alpine Linux 3.3, dropbear-2016.72-r0


## Usage
```
docker-compose up -d
docker-compose logs -f
docker-compose down
```



## To maintenance image

### interactive shell
```
docker ps -a
docker exec -it CONTAINER_ID /bin/bash

// quick
docker exec -it `docker ps -a | grep 'Up' | grep ttssh-sshd_openssh | awk '{print $1}'` /bin/bash
docker exec -it `docker ps -a | grep 'Up' | grep ttssh-sshd_twisted | awk '{print $1}'` /bin/bash
docker exec -it `docker ps -a | grep 'Up' | grep ttssh-sshd_dropbear | awk '{print $1}'` /bin/sh
```

### build
```
docker-compose build --no-cache
```



## Reference
- https://osdn.net/projects/ttssh2/ticket/43469#comment:1412:43469:1640703408
- https://srad.jp/~doda/journal/496983/
- https://srad.jp/~doda/journal/506507/
- https://srad.jp/~doda/journal/508326/
- ttssh2-devel 3941
  - https://osdn.net/projects/ttssh2/ticket/36306
