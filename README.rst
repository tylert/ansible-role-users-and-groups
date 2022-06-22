Users and Groups Role
=====================

Variables::

    %YAML 1.2
    ---

    delta_groups:

      - name: pink

      - { name: blue, gid: '1999' }
      - { name: colours, system: yes }
      - { name: shades, state: absent }

    delta_users:

      - name: blue
        group: blue
        groups: colours,users,sudo
        comment: Blue User,,,  # GECOS info
        uid: '1999'

      - name: purple
        group: purple
        groups: colours
        password: $6$asdfasdf$...

      - name: placeholder
        create_home: no

      - { name: yellow, state: absent, remove: yes, force: yes }

      - name: root
        password: '!*'
        password_lock: yes

    # - name: debianadmin
    #   groups: audio,bluetooth,cdrom,dip,floppy,lpadmin,netdev,plugdev,scanner,users,video
    # ...

    # - name: ubuntuadmin
    #   groups: adm,cdrom,dialout,dip,fax,floppy,lpadmin,plugdev,sambashare,tape,users,video
    # ...

    # - name: raspbianadmin
    #   groups: adm,audio,cdrom,dialout,games,gpio,i2c,input,netdev,plugdev,spi,sudo,users,video
    # ...

    # - name: archadmin
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

      - path: /etc/sudoers.d/red
        block: |
          Defaults:red !requiretty
          red ALL=(ALL) NOPASSWD:ALL
        create: yes
        mode: '0440'
        validate: visudo --quiet --check --file=%s

      - path: /etc/sudoers.d/green
        block: |
          green ALL=(ALL) ALL
        create: yes
        mode: '0440'
        validate: visudo -q -c -f %s

      - path: /etc/sudoers.d/blue
        block: |
          blue ALL=(ALL:ALL) ALL
        create: yes
        mode: '0440'

      - path: /etc/sudoers.d/purple
        block: |
          purple ALL=(ALL:ALL) ALL
          purple ALL=NOPASSWD: /usr/bin/foo
        create: yes
        mode: '0440'

Examples::

    # Playbook
    ansible-playbook \
        --ask-become-pass \
        --become \
        --extra-vars=@my_vars.yml \
        --inventory=inventories/staging/ \
        --user=bob \
        start.yml

    # Ad-hoc with SSH key
    ansible localhost \
        --args=tasks/main.yml \
        --extra-vars=@my_vars.yml
        --key-file=~/.ssh/id_rsa_foo \
        --module-name=import_tasks \
        --user=bob

    # Ad-hoc without SSH key
    ansible all \
        --args=tasks/main.yml \
        --extra-vars=ansible_password=armpit
        --extra-vars=@my_vars.yml \
        --inventory=10.0.0.1, \
        --module-name=include_tasks \
        --user=root

* https://github.com/ansible/ansible/pull/43131
* `https://raymii.org/s/tutorials/Ansible_-_Only_if_a_file_exists_or_does_not_exist.html`
* `https://raymii.org/s/tutorials/Ansible_-_Sudo_Safety_and_Sanity_Checks.html`
* https://serverfault.com/questions/901491/checking-sudoers-d-files-with-ansible
* https://github.com/wtcross/ansible-sudoers/blob/master/tasks/main.yml
* https://stackoverflow.com/a/41837196
* https://leucos.github.io/ansible-files-layout
