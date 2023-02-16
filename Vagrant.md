### create vagrant file

```bash
# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
    #on va definir var qui contient un script de shell de maniere explicite commune pour tout les machines presque
    common = <<-SHELL
    sudo apt update -qq 2>&1 >/dev/null
    sudo apt install -y -qq git vim tree net-tools telnet git python3-pip sshpass nfs-common 2>&1 >/dev/null
    curl -fsSL https://get.docker.com -o get-docker.sh 2>&1
    sudo sh get-docker.sh 2>&1 >/dev/null
    sed -i 's/ChallengeResponseAuthentication no/ChallengeResponseAuthentication yes/g' /etc/ssh/sshd_config
    sudo service ssh restart
    SHELL


        config.vm.define "lb1" do |lb1|
           lb1.vm.boot_timeout = 1000000000

           lb1.vm.box = "ubuntu/trusty64"
           lb1.vm.provision :shell, :inline => common
           lb1.vm.provision "ansible" do |ansible|
             #  ansible.limit = "all"
             #  ansible.verbose = "v"
             ansible.playbook = "playbook.yml"
             ansible.inventory_path = "./inventory"
           end
           lb1.vm.network "private_network", ip:"192.168.56.10"
           lb1.vm.provider "virtualbox" do |vb|
             vb.memory = "6000"
             vb.cpus = 2
           end
        end

        config.vm.define "web1" do |web1|
           web1.vm.boot_timeout = 1000000000


           web1.vm.box = "ubuntu/trusty64"
           web1.vm.provision :shell, :inline => common
           web1.vm.provision "ansible" do |ansible|
             #  ansible.limit = "all"
             #  ansible.verbose = "v"
             ansible.playbook = "playbook.yml"
             ansible.inventory_path = "./inventory"
           end
           lb1.vm.network "private_network", ip:"192.168.56.10"
           lb1.vm.provider "virtualbox" do |vb|
             vb.memory = "6000"
             vb.cpus = 2
           end
        end

        config.vm.define "web1" do |web1|
           web1.vm.boot_timeout = 1000000000


           web1.vm.box = "ubuntu/trusty64"
           web1.vm.provision :shell, :inline => common
           web1.vm.provision "ansible" do |ansible|
             #  ansible.limit = "all"
             #  ansible.verbose = "v"
             ansible.playbook = "playbook.yml"
             ansible.inventory_path = "./inventory"
           end
           web1.vm.network "private_network", ip:"192.168.56.11"
           web1.vm.provider "virtualbox" do |vb|
             vb.memory = "6000"
             vb.cpus = 2

           end
        end

        config.vm.define "web2" do |web2|
           web2.vm.boot_timeout = 1000000000


           web2.vm.box = "ubuntu/trusty64"
           web2.vm.provision :shell, :inline => common
           web2.vm.provision "ansible" do |ansible|
             #  ansible.limit = "all"
             #  ansible.verbose = "v"
             ansible.playbook = "playbook.yml"
             ansible.inventory_path = "./inventory"
           end
           web2.vm.network "private_network", ip:"192.168.56.12"
           web2.vm.provider "virtualbox" do |vb|
             vb.memory = "6000"
             vb.cpus = 2

           end

        end
end
```


### playbook.yml

```bash
---
- hosts: lb1
  become: true
  tasks:
   - name: Install Some Package on Debian Linux
     apt:
       name: '{{item}}'
       state: latest
     with_items:
       - vim
       - git
       - zip
       - gzip
       - apache2
       - docker.io
     when: ansible_os_family == "Debian"
#   - name: Install docker-compose
#     remote_user: ubuntu
#     get_url:
#       url : https://github.com/docker/compose/releases/download/1.25.1-r>
#       dest: /usr/local/bin/docker-compose
#       mode: 'u+x,g+x'


- hosts: web2
  become: true
  tasks:
   - name: Install Some Package on Debian Linux
     apt:
       name: '{{item}}'
       state: latest
     with_items:
       - vim
       - git
       - zip
       - gzip
       - apache2
       - docker.io
     when: ansible_os_family == "Debian"
 #  - name: Install docker-compose
 #    remote_user: ubuntu
```

### inventory

```bash
[lb1]
192.168.56.10 ansible_ssh_pass=vagrant  ansible_ssh_user=vagrant
[web1]
192.168.56.11 ansible_ssh_pass=vagrant  ansible_ssh_user=vagrant
[web2]
192.168.56.12 ansible_ssh_pass=vagrant  ansible_ssh_user=vagrant
```

```bash
vagrant up
```

### test connectivity

```bash
ansible all -i inventory -m ping
```

