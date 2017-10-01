# vagrant-coreos-kubernetes

Cluster for work with Kubernetes on CoreOS

### Getting start

```
# Install dependencies
vagrant plugin install vagrant-env

# Run
mv .env.example .env
vagrant up
```

### Kubernetes

Use [k8s-generator-config](https://github.com/batazor/k8s-generator-config) for run K8S.

### Setting

For settings use file `.env`

| **NAME**              | **Default**   | **Description**                      |
|-----------------------|---------------|--------------------------------------|
| NUM_INSTANCES         | 1             | count machine                        |
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
