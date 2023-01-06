## Step Installation
* Clone repository
```
git clone https://github.com/zufardhiyaulhaq/kubernetes-hardway-ansible.git
cd kubernetes-hardway-ansible
```

* Change some Vagrant variable if needed
```
vi Vagrantfile
```

* Start Vagrant
```
vagrant up
```

* SSH to deployer node
```bash
ssh vagrant@192.168.1.30
```

* Make sure deployer have access into all nodes
```bash
ssh-keygen

# copy to deployer itself
sshpass -p "vagrant" ssh-copy-id -o StrictHostKeyChecking=no vagrant@192.168.1.30

# copy to etcd & master node
sshpass -p "vagrant" ssh-copy-id -o StrictHostKeyChecking=no vagrant@192.168.1.10
sshpass -p "vagrant" ssh-copy-id -o StrictHostKeyChecking=no vagrant@192.168.1.11
sshpass -p "vagrant" ssh-copy-id -o StrictHostKeyChecking=no vagrant@192.168.1.12

# copy to master node
sshpass -p "vagrant" ssh-copy-id -o StrictHostKeyChecking=no vagrant@192.168.1.20
sshpass -p "vagrant" ssh-copy-id -o StrictHostKeyChecking=no vagrant@192.168.1.21
sshpass -p "vagrant" ssh-copy-id -o StrictHostKeyChecking=no vagrant@192.168.1.22
```

* disable ansible hostkey checking
```bash
vi ~/.ansible.cfg

[defaults]
host_key_checking = False
```

* Clone this repository
```bash
git clone https://github.com/zufardhiyaulhaq/kubernetes-hardway-ansible.git
cd kubernetes-hardway-ansible

git fetch --all
git checkout --track origin/<TAG>
```

* Adjust variable in the group_vars
```
vi group_vars/all.yml
```

* Adjust Kubernetes host and nodes
```
vi hosts/hosts
```

* Run ansible
```
ansible-playbook main.yml -i hosts/hosts
```

* Copy client.kubeconfig from deployer nodes and merge with your kubeconfig
This needed if you want to access kubernetes from other node rather than deployer mode
```
mkdir /tmp/kubeconfig
scp vagrant@192.168.1.30:~/.kube/config /tmp/kubeconfig/config

cp ~/.kube/config ~/.kube/config.bak

KUBECONFIG=~/.kube/config:/tmp/kubeconfig/config kubectl config view --flatten > /tmp/merge
cp /tmp/merge ~/.kube/config
```
