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
ssh ubuntu240.117.0.157
```

* Make sure deployer have access into all nodes
```bash
ssh-keygen

# copy to deployer itself
sshpass -p "ubuntu" ssh-copy-id -o StrictHostKeyChecking=no ubuntu240.117.0.157

# copy to etcd & master node
sshpass -p "ubuntu" ssh-copy-id -o StrictHostKeyChecking=no ubuntu240.158.0.137 
sshpass -p "ubuntu" ssh-copy-id -o StrictHostKeyChecking=no ubuntu240.186.0.156
sshpass -p "ubuntu" ssh-copy-id -o StrictHostKeyChecking=no ubuntu240.117.0.121

# copy to master node
sshpass -p "ubuntu" ssh-copy-id -o StrictHostKeyChecking=no ubuntu240.158.0.46
sshpass -p "ubuntu" ssh-copy-id -o StrictHostKeyChecking=no ubuntu240.186.0.206
sshpass -p "ubuntu" ssh-copy-id -o StrictHostKeyChecking=no ubuntu240.184.0.116
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
scp ubuntu240.117.0.157:~/.kube/config /tmp/kubeconfig/config

cp ~/.kube/config ~/.kube/config.bak

KUBECONFIG=~/.kube/config:/tmp/kubeconfig/config kubectl config view --flatten > /tmp/merge
cp /tmp/merge ~/.kube/config
```
