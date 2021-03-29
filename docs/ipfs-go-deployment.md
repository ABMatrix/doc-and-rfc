# Setup private IPFS network

*Author: Nathan Yang*

## Prerequisites
 
### Install go
```shell
wget https://studygolang.com/dl/golang/go1.15.7.linux-amd64.tar.gz
sudo tar -C /usr/local -xzf go1.15.7.linux-amd64.tar.gz

# edit .bash_profile
vim ~/.bash_profile

# go to the bottom of the file and insert the followng text
export PATH=$PATH:/usr/local/go/bin

export GOPATH=$HOME/go
export PATH=$PATH:$GOPATH/bin

# create your gopath under the home directory
mkdir $HOME/go

# activate go
source ~/.bash_profile

# other useful go env settings
# both are needed in order not to encounter go command errors
go env -w GO111MODULE=on
go env -w GOPROXY=https://goproxy.cn,direct
```

The above example shows how to install `go1.15.7`, but feel free to install the latest version of `go`.

### Install IPFS
Preferably, installing IPFS from the source is a better idea

```shell
wget -c -t 0 --no-check-certificate https://github.com/ipfs/go-ipfs/releases/download/v0.7.0/go-ipfs_v0.7.0_linux-amd64.tar.gz
tar -xzvf go-ipfs_v0.7.0_linux-amd64.tar.gz 
cd go-ipfs/
sudo ./install.sh
```


### Install IPFS-Cluster
```shell
mkdir -p $HOME/go/src/github.com/ipfs; cd $HOME/go/src/github.com/ipfs/
git clone https://github.com/ipfs/ipfs-cluster.git
cd ipfs-cluster/
go install ./cmd/ipfs-cluster-ctl
go install ./cmd/ipfs-cluster-service
go install ./cmd/ipfs-cluster-follow
```

### Install Docker and `docker-compose`
```shell
# ubuntu version to install docker
sudo apt-get update
sudo apt-get install     apt-transport-https     ca-certificates     curl     gnupg-agent     software-properties-common
curl -fsSL https://mirrors.ustc.edu.cn/docker-ce/linux/ubuntu/gpg | sudo apt-key add -
sudo apt-key fingerprint 0EBFCD88
sudo add-apt-repository    "deb [arch=amd64] https://mirrors.ustc.edu.cn/docker-ce/linux/ubuntu/ \
  $(lsb_release -cs) \
  stable"
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io

# centos version to install docker
sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
sudo yum install docker-ce

## install docker-compose
sudo curl -L https://github.com/docker/compose/releases/download/1.28.2/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose

# start and enable docker upon system reboot
sudo systemctl start docker
sudo systemctl enable docker

# run docker without sudo
sudo groupadd docker
sudo gpasswd -a $USER docker
sudo usermod -aG docker $USER

## restart docker
sudo systemctl restart docker

## log out and log back in to test...
```

## Start IPFS

### Initialize ipfs and ipfs daemon
```shell
ipfs init
export LIBP2P_FORCE_PNET=1
ipfs daemon init
```


### Generate swarm key **ONLY** for the master IPFS node

```shell
go get -u github.com/Kubuxu/go-ipfs-swarm-key-gen/ipfs-swarm-key-gen
ipfs-swarm-key-gen > ~/.ipfs/swarm.key
```


### Generate and set up `CLUSTER_SECRET` environment variable

The following command runs **only** once throughout all nodes so that nodes under the cluster know each other
`export CLUSTER_SECRET=$(od -vN 32 -An -tx1 /dev/urandom | tr -d ' \n')`

### Start IPFS-Cluster
```shell
# initialize with RAFT-based consensus
ipfs-cluster-service init --consensus raft

# start IPFS
############################
############NOTE############
# This step differs between the bootstrap node and the rest nodes
############################
## for the bootstrap node
ipfs-cluster-service daemon

## for non-bootstrap node
## note, in our case, we have a fixed port 9096 open
## e.g.,
## ipfs-cluster-service daemon --bootstrap /ip4/113.215.188.40/tcp/9096/ipfs/QmYe4arGjA48CBnNeEQZ7UNbYMr9eTvzn231taVBVCxakz
ipfs-cluster-service daemon --bootstrap <multiaddr>

```

### Troubleshootings
1. if you encounter the situation where the cluster daemon can't start successfully or where you see can't run the `ipfs-cluster-ctl` command properly, it's most likely that there's a messy raft state. In this case, you may want to stop the cluster and then run `ipfs-cluster-service state cleanup`. Finally, start the cluster again. **TODO:** we may want to be more intelligent on restarting the cluster process.
2. to avoid getting the error saying `dial tcp 127.0.0.1:5001: connect: connection refused`, modify the `.ipfs-cluster/service.json` file to have the following fields correctly set

	```json
	...
	"listen_multiaddress": "/ip4/192.168.x.y/tcp/9095",
	...
	"http_listen_multiaddress": "/ip4/0.0.0.0/tcp/9094"
	...
	"node_multiaddress": "/ip4/192.168.x.y/tcp/5001",
	...
		
	```
3. the following IPFS configuration settings are needed in order for HTTP requests to be processed

	```shell
	ipfs config --json API.HTTPHeaders.Access-Control-Allow-Methods '["PUT", "GET", "POST", "OPTIONS"]'
	ipfs config --json API.HTTPHeaders.Access-Control-Allow-Origin '["*"]'
	ipfs config --json API.HTTPHeaders.Access-Control-Allow-Headers '["Authorization"]'
	ipfs config --json API.HTTPHeaders.Access-Control-Expose-Headers '["Location"]'
	```


### Start a container with Go
```shell
# use a high version of go
sudo docker run --rm -it golang:1.15-alpine sh

# Run these inside the container
apk add git
go get github.com/whyrusleeping/ipfs-key
ipfs-key | base64 | tr -d ' \n' && echo ""

```

### Disable firewall
`sudo systemctl stop firewalld`

## Miscellaneous
The following is to generate IPFS `ID` and `private_key` and may be used in `.ipfs-cluster/service.json`

```shell
# get ipfs-key
go get github.com/whyrusleeping/ipfs-key

# generate
ipfs-key | base64 | tr -d ' \n' && echo ""
```
However, once you've initialized an IPFS cluster service by running `ipfs-cluster-service init --consensus raft` for example you don't need to run the `ipfs-key` command and can easily run `ipfs-cluster-ctl id` to inquire the corresponding IPFS cluster node ID.

One may need to change the IPFS config file in order to boost the performance of **IPNS**, you may need to do the following. (This recommendation is introduced since _go-ipfs\_0.4.18_)

First, under the `Addresses` settings, change the `NoAnnounce` array to be an empty array.

So, this:

```json
"Addresses": {
  "NoAnnounce": [
    "/ip4/10.0.0.0/ipcidr/8",
    "/ip4/100.64.0.0/ipcidr/10",
    "/ip4/169.254.0.0/ipcidr/16",
    "/ip4/172.16.0.0/ipcidr/12",
    "/ip4/192.0.0.0/ipcidr/24",
    "/ip4/192.0.0.0/ipcidr/29",
    "/ip4/192.0.0.8/ipcidr/32",
    "/ip4/192.0.0.170/ipcidr/32",
    "/ip4/192.0.0.171/ipcidr/32",
    "/ip4/192.0.2.0/ipcidr/24",
    "/ip4/192.168.0.0/ipcidr/16",
    "/ip4/198.18.0.0/ipcidr/15",
    "/ip4/198.51.100.0/ipcidr/24",
    "/ip4/203.0.113.0/ipcidr/24",
    "/ip4/240.0.0.0/ipcidr/4"
  ]
}
```
Becomes this:

```json
"Addresses": {
  "NoAnnounce": [
  ]
}
```
Then, under your `Swarm` settings, change the `AddrFilters` array to null.

So, this:

```json
"Swarm": {
  "AddrFilters": [
    "/ip4/10.0.0.0/ipcidr/8",
    "/ip4/100.64.0.0/ipcidr/10",
    "/ip4/169.254.0.0/ipcidr/16",
    "/ip4/172.16.0.0/ipcidr/12",
    "/ip4/192.0.0.0/ipcidr/24",
    "/ip4/192.0.0.0/ipcidr/29",
    "/ip4/192.0.0.8/ipcidr/32",
    "/ip4/192.0.0.170/ipcidr/32",
    "/ip4/192.0.0.171/ipcidr/32",
    "/ip4/192.0.2.0/ipcidr/24",
    "/ip4/192.168.0.0/ipcidr/16",
    "/ip4/198.18.0.0/ipcidr/15",
    "/ip4/198.51.100.0/ipcidr/24",
    "/ip4/203.0.113.0/ipcidr/24",
    "/ip4/240.0.0.0/ipcidr/4"
  ]
```
Becomes this:

```json
"Swarm": {
    "AddrFilters": null,
```

## Reference
1. [IPFS cluster deployment setup from the official site](https://cluster.ipfs.io/documentation/deployment/setup/)
2. [Distributed Web: host your website with IPFS clusters, Cloudflare, and DevOps](https://withblue.ink/2018/11/14/distributed-web-host-your-website-with-ipfs-clusters-cloudflare-and-devops.html)
3. [IPFS Tutorial: Building a Private IPFS Network with IPFS-Cluster for Data Replication](https://labs.eleks.com/2019/03/ipfs-network-data-replication.html)