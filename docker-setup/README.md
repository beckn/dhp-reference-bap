# dhp-reference-bap
Reference BAP using DHP created from BIAB reference BAP.

We will need to EC2 machines for two setups one for API Layer and another for UI Layer.

> **Prereuisites for EC2 Machines:**
>
>    For more information on installing Docker please refer to the
   [Docker Guide](https://docs.docker.com/engine/installation/linux/

>  We also need certbot to be installed.

```bash
$ sudo yum install -y certbot
```

## Setup 1 . Preparing setup for Client and Protcol layer 

Set Up instaructions for Client and Protocol layer 

Inside docker-setup directroy 

We need first build base dtos layer so that client and protocol layer can use the DTOs jar.
```bash
$ docker build -t bap-base -f base.dockerfile .
```

Once we have base image build we can move forward and generate certifactes.

Specify server name in nginx/data/nginx/app.conf file and use certbot to generate SSL certs, for example we specified api.healthcarebap.becknprotocol.io;
Change the same ofr other entries in app.conf

```bash
# generate certs
$ sudo certbot certonly --standalone --preferred-challenges http -d  api.healthcarebap.becknprotocol.io 
# list certs 
$ cd /etc/letsencrypt/archive/api.healthcarebap.becknprotocol.io
cert1.pem  chain1.pem  fullchain1.pem  privkey1.pem
# rename certs and copy for use
$ mv cert1.pem cert.pem; mv chain1.pem chain.pem ; mv fullchain1.pem fullchain.pem; mv privkey1.pem privkey.pem
$ sudo cp etc/letsencrypt/archive/api.healthcarebap.becknprotocol.io data/certbot/conf/live/
$ sudo chown -R ec2-user:ec2-user data
```

Bring Protcol-helper and Client layer

```bash
$ cd docker-setup/proto-client-layer
$ docker-compose up -d 
```


## Setup 2. Preparing Setup for UI-Layer

One machines where we run storefront setup

Specify server name in nginx/data/nginx/app.conf file and use certbot to generate SSL certs, for example we specified healthcarebap.becknprotocol.io;
Change the other entries in app.conf

```bash
# generate certs
$ sudo certbot certonly --standalone --preferred-challenges http -d  healthcarebap.becknprotocol.io 
# list certs
$ cd /etc/letsencrypt/archive/healthcarebap.becknprotocol.io
cert1.pem  chain1.pem  fullchain1.pem  privkey1.pem

# rename certs and copy for use
$ mv cert1.pem cert.pem; mv chain1.pem chain.pem ; mv fullchain1.pem fullchain.pem; mv privkey1.pem privkey.pem
$ cd docker-setup/ui-layer
$ sudo cp etc/letsencrypt/archive/healthcarebap.becknprotocol.io data/certbot/conf/live/
$ sudo chown -R ec2-user:ec2-user data
```

# Replace MAPS_KEY in docker-compose.yml and Bring UI Layer

```bash
$ cd docker-setup/ui-layer
$ docker-compose up -d 
```
