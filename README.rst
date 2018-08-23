Ansible 2.6.3 (2.7.x!!!)

vars.yml::

    ---

    delta_groups:

      - name: blue
        gid: 1000

      - name: colours
        system: yes

      - name: shades
        state: absent

    delta_users:

      - name: blue
        comment: Blue User,,,  # GECOS info
        group: blue
        groups: colours,users,sudo
        uid: 1000

      - name: placeholder
        create_home: no

      - name: yellow
        state: absent
        remove: yes
        force: yes

      - name: root
        password: '!!'
        password_lock: yes

    delta_authorized_keys:

      - user: blue
        key: ssh-rsa AAAAB3NzaC1yc2EAAAADAQ...
        exclusive: yes

::

    ansible-playbook ... --extra-vars @vars.yml

* https://github.com/ansible/ansible/pull/43131
