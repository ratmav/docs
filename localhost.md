localhost
=========

## macos

* brave: `brew install --cask brave-browser`
* docker: `brew install --cask docker`
* signal: `brew install --cask signal`
* vagrant: `brew install --cask vagrant`
* virtualbox: `bew install --cask virtualbox`

## debian

### brave

```shell
sudo apt install apt-transport-https curl gnupg

curl -s https://brave-browser-apt-release.s3.brave.com/brave-core.asc | sudo apt-key --keyring /etc/apt/trusted.gpg.d/brave-browser-release.gpg add -

echo "deb [arch=amd64] https://brave-browser-apt-release.s3.brave.com/ stable main" | sudo tee /etc/apt/sources.list.d/brave-browser-release.list

sudo apt update

sudo apt install brave-browser
```

### docker

* https://docs.docker.com/engine/install/debian/
* https://docs.docker.com/compose/install/

is there an official compose in the docker repo?

### lutris

https://lutris.net/downloads/

### signal

https://signal.org/download/#

### steam

https://wiki.debian.org/Steam

### vagrant

https://www.hashicorp.com/blog/announcing-the-hashicorp-linux-repository

### virtualbox

https://tecadmin.net/install-virtualbox-on-debian-10-buster/
