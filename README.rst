Users and Groups Role
=====================

my_vars.yml::

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

      # - name: debianadmin
      #   groups: audio,bluetooth,cdrom,dip,floppy,lpadmin,netdev,plugdev,scanner,users,video
      # ...

      # - name: ubuntuadmin
      #   groups: adm,cdrom,dialout,dip,fax,floppy,lpadmin,plugdev,sambashare,tape,users,video
      # ...

      # - name: raspbianadmin
      #   groups: adm,audio,cdrom,dialout,dip,games,gpio,i2c,input,lpadmin,netdev,plugdev,spi,sudo,users,video
      # ...

      # - name: antergosadmin
      #   groups: users,wheel
      # ...

    delta_authorized_keys:

      - user: blue
        key: ssh-rsa AAAAB3NzaC1yc2EAAAADAQ...
        exclusive: yes

      - user: red
        key: ssh-rsa AAAAB3NzaC1yc2EAAAADAQ...
        state: absent

    delta_sudoers:

      - block: |
          Defaults:red !requiretty
          red ALL=(ALL) NOPASSWD: ALL
        path: /etc/sudoers.d/red
        create: yes
        mode: '0440'
        validate: visudo --quiet --check --file=/etc/sudoers.d/red

      - block: |
          green ALL=(ALL) ALL
        path: /etc/sudoers.d/green
        create: yes
        mode: '0440'
        validate: visudo --quiet --check --file=/etc/sudoers.d/green

      - block: |
          blue ALL=(ALL:ALL) ALL
        path: /etc/sudoers.d/blue
        create: yes
        mode: '0440'
        validate: visudo --quiet --check --file=/etc/sudoers.d/blue

Examples::

    ansible-playbook --become -i 10.0.0.1, start.yml \
        --extra-vars=ansible_user=bob \
        --extra-vars @my_vars.yml

    ansible localhost --user=bob \
        --module-name=import_tasks \
        --args=tasks/main.yml \
        --extra-vars=@defaults/main.yml \
        --extra-vars=@my_vars.yml

    ansible all --inventory server, --become --ask-pass \
        --module-name=import_tasks \
        --args=tasks/main.yml \
        --extra-vars=@defaults/main.yml \
        --extra-vars=@my_vars.yml \
        --extra-vars='ansible_user=armpit'

* https://github.com/ansible/ansible/pull/43131
* `https://raymii.org/s/tutorials/Ansible_-_Only_if_a_file_exists_or_does_not_exist.html`
* `https://raymii.org/s/tutorials/Ansible_-_Sudo_Safety_and_Sanity_Checks.html`
* https://serverfault.com/questions/901491/checking-sudoers-d-files-with-ansible
* https://github.com/wtcross/ansible-sudoers/blob/master/tasks/main.yml
* https://stackoverflow.com/a/41837196
* https://leucos.github.io/ansible-files-layout
