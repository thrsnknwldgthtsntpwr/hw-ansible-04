Vector-role
=========

This role allows you to install Vector on CentOS 8+.

Role Variables
--------------

| vars | Description | Value | Location |
|------|------------|---|---|
| vector_version | Vector version to install | "0.42.0" | defaults/main.yml |
| vector_rpm_version | Vector rmp version to use for installation | "0.42.0-1" | defaults/main.yml |
| vector_clickhouse_ip | An instance for Clickhouse to work with Vector | for ex., "localhost" | defaults/main.yml |
| clickhouse_db_name | Clickhouse DB name for logs | "logs"  | defaults/main.yml |
| clickhouse_table_name | Clickhouse table name to write down logs | "data_logs" | defaults/main.yml |
| vector_config | Vector configuration settings | wwritten down below, may be changed | default/main.yml |
| vector_url | URL for Vector packages | "https://yum.vector.dev/stable/vector-0/x86_64/vector-{{ vector_rpm_version }}.x86_64.rpm" | vars/main.yml |
| vector_config_dir | Vector config file location | "/etc/vector" | vars/main.yml |

Example Playbook
----------------

```yml
---
- name: Get vector distrib
  ansible.builtin.get_url:
        url: "https://yum.vector.dev/stable/vector-0/x86_64/vector-{{ vector_rpm_version }}.x86_64.rpm"
        dest: "./vector-{{ vector_version }}-1.x86_64.rpm"
        mode: "0644"
- name: Install vector packages
  become: true
  ansible.builtin.yum:
        name:
          - vector-{{ vector_version }}-1.x86_64.rpm
        disable_gpg_check: true
- name: Apply vector template
  become: true
  ansible.builtin.template:
    src: templates/vector.yml.j2
    dest: "{{ vector_config_dir }}/vector.yml"
    mode: "0644"
    owner: "{{ ansible_user_id }}"
    group: "{{ ansible_user_gid }}"
    validate: vector validate --no-environment --config-yaml %s
- name: Create vector systemd unit
      become: true
      ansible.builtin.template:
        src: templates/vector.service.j2
        dest: /usr/lib/systemd/system/vector.service
        mode: "0644"
        owner: "{{ ansible_user_id }}"
        group: "{{ ansible_user_gid }}"
        backup: true
      notify: Start vector service

```

License
-------

MIT

Author Information
------------------

Andrey Seregin
