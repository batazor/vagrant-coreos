# vagrant-coreos

Cluster for work with Kubernetes on CoreOS

### Getting start

```
# Clone project
git clone https://github.com/batazor/vagrant-coreos.git
cd vagrant-coreos

# Install dependencies
vagrant plugin install vagrant-env

# Run
cp .env.example .env
vagrant up

# Connect (example)
ssh core@172.17.7.101
```

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
