Role Name
=========

Скачивание lighthouse из официального репозитория на github.com для centos7.

Requirements
------------

Зависимостей не покрываемых Ansible или role нет.

Role Variables
--------------

Используемые переменные находятся в defaults:
```yaml
---
lighthouse_location_dir: "/home/centos/lighthouse"
lighthouse_access_log_name: "lighthouse_access"
nginx_user_name: "root"
```
И в vars:
```yaml
---
lighthouse_repo: "https://github.com/VKCOM/lighthouse.git"
lighthouse_repo_version: "master"
```

Dependencies
------------

Зависимостей от других ролей нет.
Пакеты git, epel-release и nginx устанавливаются в рамках этой роли.

Example Playbook
----------------
Play установки Lighthouse с включёнными зависимостями.
```yaml
---
- include_tasks: git.yml
- include_tasks: nginx.yml
- name: Download Lighthouse
  ansible.builtin.git:
    repo: "{{ lighthouse_repo }}"
    version: "{{ lighthouse_repo_version }}"
    dest: "{{ lighthouse_location_dir }}"
  tags:
    - lh-down

- name: Create LIGHTHOUSE config
  become: true
  ansible.builtin.template:
    src: lighthouse.conf.j2
    dest: /etc/nginx/conf.d/default.conf
    mode: 0644
  notify: restart-nginx
- name: SELinux dynamic disable
  become: true
  ansible.builtin.command: "setenforce 0"
  notify: restart-nginx
```

Play установки Git
```yaml
---
- name: Install GIT
  become: true
  ansible.builtin.yum:
    name: git
    state: present
  tags:
    - git-inst
```

Play установки NGINX
```yaml
---
- name: Install NGINX | Install EPEL repository for NGINX
  become: true
  ansible.builtin.yum:
    name: epel-release
    state: present
  ignore_errors: true

- name: Install NGINX
  become: true
  ansible.builtin.yum:
    name: nginx
    state: present
  notify: restart-nginx
  ignore_errors: true

- name: Create NGINX config
  become: true
  ansible.builtin.template:
    src: nginx.conf.j2
    dest: /etc/nginx/nginx.conf
    mode: 0644
  notify: restart-nginx
  ignore_errors: true
```

License
-------

BSD

Author Information
------------------

[LightHouse](https://github.com/vkcom/lighthouse)

Role by [Andrey Mishin](https://github.com/andrey-mishin)
