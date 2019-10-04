docker
======

[documentation](https://docs.docker.com/)

# private registry build

## host details

* os: **centos 7**
* user: `root`

## install docker

```bash
$ yum update # update packages.
$ yum install -y htop # better top, ymmv.
$ rpm --import http://dl.fedoraproject.org/pub/epel/rpm-gpg-key-epel-7 # import epel gpg key.
$ yum install -y epel-release # enable epel.
$ curl -ssl https://get.docker.com/ | sh # install docker.
$ systemctl start docker # start docker.
$ systemctl enable docker # start docker on boot.
$ docker run hello-world # test docker install.
```

## configure registry

**note**: configured to use aws s3 for image storage.

config file example:

```yaml
# /root/config.yml
version: 0.1
log:
  fields:
    service: registry
storage:
  s3:
    accesskey: # aws key id
    secretkey: # aws secret key
    region: us-west-1
    bucket: your-s3-bucket
http:
    addr: :5000
    headers:
        x-content-type-options: [nosniff]
health:
  storagedriver:
    enabled: true
    interval: 10s
    threshold: 3
```

## authentication

* user: `your-docker-user`
* pass: `your-docker-password`

```bash
$ mkdir auth
$ docker run --entrypoint htpasswd registry:2 -bbn your-docker-user your-docker-password > auth/htpasswd
```

## start and create registry container

```bash
# auth and tls information are included in the container as mounted volumes.
$ docker run -d -p 5000:5000 --restart=always --name registry \
  -v `pwd`/auth:/auth \
  -e "registry_auth=htpasswd" \
  -e "registry_auth_htpasswd_realm=registry realm" \
  -e registry_auth_htpasswd_path=/auth/htpasswd \
  -v /root/config.yml:/etc/docker/registry/config.yml \
  -v /root/certs:/certs \
  -e registry_http_tls_certificate=/certs/private.registry.com.crt \
  -e registry_http_tls_key=/certs/private.registry.com.key \
  registry:2
```

## add registry users

```bash
# stop the registry container:
$ docker stop registry
# remove the registry container:
$ docker rm registry
# add the new user:
docker run --entrypoint htpasswd registry:2 -bbn newuser newpass >> auth/htpasswd
# rebuild the registry container:
$ docker run -d -p 5000:5000 --restart=always --name registry \
  -v `pwd`/auth:/auth \
  -e "registry_auth=htpasswd" \
  -e "registry_auth_htpasswd_realm=registry realm" \
  -e registry_auth_htpasswd_path=/auth/htpasswd \
  -v /root/config.yml:/etc/docker/registry/config.yml \
  -v /root/certs:/certs \
  -e registry_http_tls_certificate=/certs/private.registry.com.crt \
  -e registry_http_tls_key=/certs/private.registry.com.key \
  registry:2
```

## remove registry users

just remove a given user's entry from the `/root/auth/htpasswd` file.
