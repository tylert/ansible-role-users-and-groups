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
        password: '!'
        password_lock: yes

    delta_authorized_keys:

      - user: blue
        key: ssh-rsa AAAAB3NzaC1yc2EAAAADAQ...
        exclusive: yes

    delta_sudoers:

      - block: |
          Defaults:red !requiretty
          red ALL=(ALL) NOPASSWD: ALL
        path: /etc/sudoers.d/red
        create: yes
        mode: '0440'

      - block: |
          green ALL=(ALL) ALL
        path: /etc/sudoers.d/green
        create: yes
        mode: '0440'

      - block: |
          red ALL=(ALL:ALL) ALL
        path: /etc/sudoers.d/red
        create: yes
        mode: '0440'

::

    ansible-playbook ... --extra-vars @vars.yml

* https://github.com/ansible/ansible/pull/43131
* https://raymii.org/s/tutorials/Ansible_-_Only_if_a_file_exists_or_does_not_exist.html
* https://raymii.org/s/tutorials/Ansible_-_Sudo_Safety_and_Sanity_Checks.html
* https://serverfault.com/questions/901491/checking-sudoers-d-files-with-ansible
* https://github.com/wtcross/ansible-sudoers/blob/master/tasks/main.yml
* https://stackoverflow.com/a/41837196
* https://leucos.github.io/ansible-files-layout
