# vagrant-coreos

Cluster for work with Kubernetes on CoreOS

### Getting start

```
# Install dependencies
sudo apt-get install nfs-common nfs-kernel-server // Install NFS-server
vagrant plugin install vagrant-env

# Run
cp .env.example .env
vagrant up
```

### Kubernetes

Use [k8s-generator-config](https://batazor.github.io/k8s-generator-config) for run K8S.

### Setting

For settings use file `.env`

| **NAME**              | **Default**   | **Description**                      |
|-----------------------|---------------|--------------------------------------|
| NUM_INSTANCES         | 3             | count machine                        |
|-----------------------|---------------|--------------------------------------|
| INSTANCE_NAME_PREFIX  | core          |                                      |
| UPDATE_CHANNEL        | stable        |                                      |
| IMAGE_VERSION         | current       |                                      |
|-----------------------|---------------|--------------------------------------|
| VM_GUI                | false         |                                      |
| VM_MEMORY             | 1024          |                                      |
| VM_CPUS               | 1             |                                      |
| VB_CPUEXECUTIONCAP    | 100           |                                      |
|-----------------------|---------------|--------------------------------------|
| ETCD_IMAGE_TAG        | v3.2.8        | Version use ETCD                     |
|-----------------------|---------------|--------------------------------------|
