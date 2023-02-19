### playbook
```bash
---
- hosts: lb1
  become: true
  tasks: 
  - name: intall docker
    apt:
    - name: docker.io
      state: latest
  - name: start docker service
    service:
    - name: docker
      state: started
      enabled: yes
  - name: Install software for pip3 
    package:
    - name: python36
      state: present
      
  - name: Install Pip docker Library 
    pip:
    - name: docker-py

  - name: Run Container
    docker_container:
      name: myos1
      image: ubuntu:14.04
      
```
