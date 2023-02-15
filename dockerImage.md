### playbook
```bash
---
- name: my first playbook
  hosts: all
  tasks:
  #L'utilisation des modules Docker d'Ansible nécessite d'installer le kit de développement (SDK) Docker
  - name: install docker sdk
    apt:
      name: python3-docker
      update_cache: yes
      # 1h
      cache_valid_time: 3600
      # sudo pour execute apt
    become: yes
    
  - name: Pull an image 
    docker_image:
      name: alpine
      tag: latest
      source: pull
      
   # re-tag an image
  - name: Pull an image 
    docker_image:
      name: alpine
      tag: latest
      repository: myregistry/monimage:v1.0
      source: local
      
# create an image from dockerfile
#1st create directory
  - name: copy files
    file:
      path: /tmp/build
      state: directory
      
#2nd we copy local dir an a target machine 
  - name: copy image
    copy:
      src: app/
      dest: /tmp/build

    
  - name: build
    docker_image:
      name: imgbuild
      tag: v1.0
      source: build 
      build:
        path: /tmp/build/app
        dockerfile: Dockerfile
        cache_from:
        - alpine:3.9
   
  
