---
layout: post
title: "How to run Panamax on Digital Ocean"
type: post
tags:
- docker
- DigitalOcean
- panamax
status: publish
published: true
---

[Panamax](http://panamax.io/) offers a simple to use UI to manage
[Docker](https://www.docker.com/) systems by leveraging community written templates.
This allows you to quickly setup a server with eg. Gitlab, or as we'll see at
the end of this blogpost, Stellard.

Panamax is built on top of [CoreOS](https://coreos.com/), the minimal OS
*for Massive Server Deployments*, for which
[DigitalOcean](https://www.digitalocean.com/) launched support [just 5 days
ago](https://www.digitalocean.com/company/blog/coreos-now-available-on-digitalocean/).
If you don't have a Digital Ocean account, you can start one [using this
link](https://www.digitalocean.com/?refcode=7bce29475383) to get a $10 bonus credit.

# Generate a discovery.etcd.io token

Go to [https://discovery.etcd.io/new](https://discovery.etcd.io/new) and write down the token.
This is the unique token your CoreOS cluster will use to discover other nodes.

# Start a new droplet

Use Digital Ocean's online interface to start a CoreOS server in a region that
supports "User Data" (NYC3, SFO1, SGP1 or LON1 at the time of writing).
Enter the following cloud config as User Data (replace the &lt;token&gt; with
your unique discovery token from the previous step).

Iets aan toevoeg.

```
#cloud-config

coreos:
  etcd:
    # generate a new token for each unique cluster from https://discovery.etcd.io/new
    discovery: https://discovery.etcd.io/<token>
    # multi-region deployments, multi-cloud deployments, and droplets without
    # private networking need to use $public_ipv4
    addr: $private_ipv4:4001
    peer-addr: $private_ipv4:7001
  units:
    - name: etcd.service
      command: start
    - name: fleet.service
      command: start
```

I had good results running Stellard on Panamax with a 2GB Droplet.

# Install panamax

1. ssh into your droplet as user core:<br>
`ssh core@<your-droplet-public-ip>`
2. download the panamax installer:<br>
`curl -O http://download.panamax.io/installer/pmx-installer-latest.zip`
3. run the installer as root:<br>
`sudo ./coreos install --stable`

When that finishes you can point your browser to &lt;your-droplet-public-ip&gt;:3000 and explore the panamax UI.

# Bonus points: Stellard

To test the panamax installation, we can install and run
[stellard](https://www.stellar.org/) on your new panamax instance. 
Go to the panamax UI at &lt;your-droplet-public-ip&gt;:3000,
search for 'stellard' and click 'Run Template':

![Panamax Stellard](/images/panamax-stellard.png)

You can now query stellard with curl:

```
curl -X POST http://<your-droplet-public-ip>:9002 -d '{ "method" : "ping" }'
```

Check the full [stellard api](https://www.stellar.org/api/) for more fun stuff you can do.
