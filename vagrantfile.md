### intialize vagrantfile
```bash
vagrant init
```
### add phath of playbook and inventory files
```bash
Vagrant.configure("2") do |config|
  #vagrant will wait for the machine to boot and be accessible
  config.vm.boot_timeout = 1000000000
  config.vm.box = "ubuntu/trusty64"
  config.vm.define "machine1"
  # Sensitive values will not show
  config.vagrant.sensitive =["MySecretPassword" , ENV["MY_TOKEN"]]
  # Run Ansible from the vagrant host
  config.vm.provision "ansible" do |ansible|
  #  ansible.limit = "all"
  #  ansible.verbose = "v"
    ansible.playbook = "playbook.yml"
    ansible.inventory_path = "./inventory"
 end 
  config.vm.network "private_network", ip:"192.168.56.10"
  config.vm.provider "virtualbox" do |vb|
    vb.memory = "1024"
  end
end
```
### install packages such as vim,git,zip,gzip,docker and docker-compose from url
```bash
---
- host: machine1
  become: true
  tasks:
  - name: Install Some Package on Debian Linux
    apt:
     name: '{{item}}'
     state: latest
    with_item:
     - vim
     - git
     - zip
     - gzip
     - apache2
     - docker.io
    when: ansible_os_family == "Debian"
  - name: Install docker-compose
    remote_user: ubuntu
    get_url: 
      url : https://github.com/docker/compose/releases/download/1.25.1-rc1/docker-compose-Linux-x86_64
      dest: /usr/local/bin/docker-compose
      mode: 'u+x,g+x'
```
### inisialize inventory
```bash
[machine1]
192.168.56.10 ansible_ssh_pass=vagrant  ansible_ssh_user=vagrant
```
### test ping
```bash
ansible all -i inventory -m ping
```
 
  
