# ansible-playbook写剧本

vim  hostname.yml

```
---
- hosts: web2
  vars: 
    ip: "{{ ansible_ens33['ipv4']['address'] }}"
  tasks: 
  - name: 将ansible的hosts复制到别的机器上
    copy: src=/tmp/hosts dest=/etc/hosts owner=root group=root mode=0644 backup=yes
  - name: lineinfile_add
    lineinfile: path=/etc/hosts line="{{ ip }} {{ ansible_hostname }}"
  - name: linelinfile2_add
    lineinfile: path=/etc/hosts line="{{ ip2 }} server"

```



vim item.yaml

```
---
- hosts: web2
  vars:
    tiger: true
  tasks:
    - shell: echo "tiger" > /tmp/when.txt
      when: tiger
    - shell: echo {{ item }} >> /tmp/when.txt
      with_items: [1,3,4,5,6,7,8,9]
      when: item > 5

```



vim many_install.yaml 

```
---
- hosts: web2
  tasks:
    - yum:
          name: [ 'tree', 'netcat' ]
          state: present

```



vim nginx.yaml 

```
---
- hosts: web2
  remote_user: root
  tasks: 
    - name: install nginx
      yum: name=nginx state=present
    - name: copy nginx
      copy: src=/tmp/nginx.conf dest=/etc/nginx/nginx.conf backup=yes
      notify: reload
      tags: restartnginx
    - name: start nginx service
      service: name=nginx state=started

  handlers: 
    - name: reload
      service: name=nginx state=restarted

```



vim test.yaml

```
 ---
- hosts: web2
  remote_user: root
  tasks: 
    - name: test1
      shell: echo 1 > /tmp/abc.txt
    - name: test_centos7
      shell: echo 2 >> /tmp/abc.txt
      when: ansible_distribution_major_version == "7"
    - name: test_centos6
      shell: echo 3 >> /tmp/abc.txt
      when: ansible_distribution_major_version == "6"

```



vim tree.yml 

```
---
- hosts: web2
  remote_user: root
  tasks:
    - name: 写入index.html
      copy: content="wo shi ni baba" dest=/usr/share/nginx/html/index.html mode=644 owner=nginx group=nginx

```



vim useradd.yaml 

```
---
- hosts: web2
  vars:
    user: jerry
  tasks:
    - name: create {{ user }}
      user: name={{ user }} state=absent remove=yes
      when: ansible_fqdn=='dda'
```

字典

```
---
- hosts: web2
  tasks:
    - name: create user and group
    - group: name={{ item }} state=present
      with_items:
          - group11
          - group12
          - group13
    - user: name={{ item.user }} group={{ item.group }} state=present
      with_items:
         - { user: 'user11', group: 'group11' }
         - { user: 'user12', group: 'group12' }
         - { user: 'user13', group: 'group13' }
```

