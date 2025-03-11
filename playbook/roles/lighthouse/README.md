Lighthouse-role
=========

This role allows you to install Lighthouse on CentOS 8+ via a [github repo](https://github.com/VKCOM/lighthouse.git).        

Requirements
------------

`Git` and `NGINX` must be pre-installed and configured before running this role. 

Role Variables
--------------

| vars | Description | Value | Location |
|------|------------|---|---|
| lighthouse_dir | a directory to store Lighthouse files | "/home/{{ ansible_user_id }}/lighthouse" | defaults/main.yml |
| lighthouse_url | URL of Lighthouse repo | "https://github.com/VKCOM/lighthouse.git" | vars/main.yml |


Example Playbook
----------------

```yml
- name: Install lighthouse
  hosts: lighthouse
  handlers:
    - name: Nginx restart
      become: true
      ansible.builtin.service:
        name: nginx
        state: restarted
  pre_tasks:
    - name: Install git for lighthouse
      become: true
      ansible.builtin.yum:
        name: git
        state: present
    - name: Install epel-release for lighthouse
      become: true
      ansible.builtin.yum:
        name: epel-release
        state: present
    - name: Install nginx for lighthouse
      become: true
      ansible.builtin.yum:
        name: nginx
        state: present
    - name: Apply nginx config for lighthouse
      become: true
      ansible.builtin.template:
        src: nginx.conf.j2
        dest: /etc/nginx/nginx.conf
        mode: "0644"
  roles:
    - lighthouse-role
  post_tasks:
    - name: Apply lighthouse config
      become: true
      ansible.builtin.template:
        src: lighthouse_nginx.conf.j2
        dest: /etc/nginx/conf.d/lighthouse.conf
        mode: "0644"
      notify: Nginx restart
```

License
-------

MIT

Author Information
------------------

Andre Seregin
