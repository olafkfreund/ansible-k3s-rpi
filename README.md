This Ansible playbook turns your fleet of Raspberry Pi into Kubernetes cattle with k3s, the streamlined version of Kubernetes by @rancher that runs silky smooth on the ARM processor powering your Pi.

# Manually prepare your Raspberries
- Install ubuntu
- configure disk (in this repo i'm using /dev/sda)

# Secure your Raspberries
Optional but advisable: use SSH key auth and disable password login.
The default ssh credentials for a Raspberry Pi are username `ubuntu` and password `ubuntu`.
- Copy your pubkey to the Pi with `ssh-copy-id ubuntu@k8smaster.home.freundcloud.com`
- In `/etc/ssh/sshd_config` on the Pi, set `PasswordAuthentication` to `no`

# Configure ansible setup
- Choose one of the Raspberries to lead / orchestrate your cluster; we'll call this the _server_
- In `inventory/hosts`, fill out the ip or hostname for the leading Raspberry under `[k8smaster]`
- Fill out the ip's or hostnames for the rest of your cattle under `[k8snodes]`


# Provision the nodes
When you're all set up and configured, run the Ansible playbook:
```bash
$ make
```

This will:
- Install the k3s binary on the 'server' Pi (the leading node)
- Install a `k3s-server` service on the server and start it
- Fetch the node token from the server
- Install and start a `k3s-agent` service on the agents, joining the cluster
- Enable autostart on boot for k3s on all nodes
- Add nodes/master IP to /etc/hosts
- Configure the nodes for ARM64
- Configure NFS server and clients and automount /mnt/disk01 on each node.

# See if it worked
Log into your server node and run:
```bash
$ sudo k3s kubectl get node -o wide
```
You should see all of your nodes broadcasting a _Ready_ status.

Happy cattle herding!
