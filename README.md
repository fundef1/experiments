# experiments

## faasd on proxmox
Create Container (CT via web UI) : image debian-10...
All standard options: (to be reviewed, just a first guess)
- uncheck unpriviled
- MEM/SWAP: 1024
- network: DHCP (i run a separate dnsmasq and manage it there)

based on https://gist.githubusercontent.com/gabeduke/7ccb3f3147d79ac30e2187432808060c/raw/4028ea0658e9aa0a37f6ac44473a8092e3824406/cloud-init.yml
from user.user_data: bumped containerd to 1.4.0 and cni plugins to 0.8.7
version numbers are hardcoded

in proxmox under options->features->enable nesting\
cfr. ```security.nesting=true```

ssh into CT\
```apt-get update && apt-get install runc```

#unused just for reference
```
FAASD-VERSION=0.9.2
FAASCLI-VERSION=0.12.9
CONTAINERD-VERSION=1.4.0
CNI-VERSION=0.8.7
DNS=10.0.0.2
```

```wget https://github.com/containerd/containerd/releases/download/v1.4.0/containerd-1.4.0-linux-amd64.tar.gz
tar -xvf ./containerd-1.4.0-linux-amd64.tar.gz -C /usr/local/bin/ --strip-components=1
#rm ./containerd-1.4.0-linux-amd64.tar.gz
wget https://raw.githubusercontent.com/containerd/containerd/release/1.4/containerd.service -P /etc/systemd/system
systemctl daemon-reload && systemctl start containerd
systemctl enable containerd
# bridging already enabled via pve
# /sbin/sysctl -w net.ipv4.conf.all.forwarding=1
mkdir -p /opt/cni/bin
wget -qO- https://github.com/containernetworking/plugins/releases/download/v0.8.7/cni-plugins-linux-amd64-v0.8.7.tgz | tar -xz -C /opt/cni/bin
# cloning repo not necessary 
#mkdir -p /go/src/github.com/openfaas/
#cd /go/src/github.com/openfaas/
# git bla removed

wget https://github.com/openfaas/faasd/releases/download/0.9.2/faasd -P /usr/local/bin/
chmod a+x /usr/local/bin/faasd
# only the yaml/yml/resove files are needed for install
wget https://raw.githubusercontent.com/openfaas/faasd/master/docker-compose.yaml
wget https://raw.githubusercontent.com/openfaas/faasd/master/prometheus.yml
# faasd install requires a "resolv.conf" is hardcoded to google dns 8.8.8.8
# replace with local dns
echo "nameserver 10.0.0.2" > resolv.conf
# could just dump this in /etc/systemd/system but faasd install wants them in 'hack'
wget https://raw.githubusercontent.com/openfaas/faasd/master/hack/faasd-provider.service -P ./hack
wget https://raw.githubusercontent.com/openfaas/faasd/master/hack/faasd.service -P ./hack

# optional status checks
#systemctl status -l containerd --no-pager
#journalctl -u faasd-provider --no-pager
#systemctl status -l faasd-provider --no-pager
#systemctl status -l faasd --no-pager
#sleep 60 && journalctl -u faasd --no-pager

# not needed but handy....
# get faas-cli 
# curl -sSLf https://cli.openfaas.com | sh
wget https://github.com/openfaas/faas-cli/releases/download/0.12.9/faas-cli -P /usr/local/bin
chmod a+x /usr/local/bin/faas-cli
#login with basic credentials 
faas-cli login --password `cat /var/lib/faasd/secrets/basic-auth-password`
```


#docker command for faas on debian/proxmox\
create CT
```apt-get update``` 
(don't just run apt-get docker)

```
#docker-ce
apt-get install gnupg
wget -q -O- https://download.docker.com/linux/debian/gpg | apt-key add -
echo "deb https://download.docker.com/linux/debian buster stable" > /etc/apt/sources.list.d/docker.list
apt update
#should try docker-ce-cli only
# apt-get install docker-ce #### don't need entire docker engine
#apt-get install -y docker-ce-cli
#gggrmmbl still needs docker running...
apt-get install docker-ce```
```

grab templates from faas store
faas-cli template store pull
