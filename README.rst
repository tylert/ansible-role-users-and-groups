Users and Groups Role
=====================

YAML examples::

    delta_groups:
      - name: pink
      - { name: blue, gid: '1999' }
      - { name: colours, system: true }
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
        password: $6$asdfasdf$...  # password hash
      - name: placeholder
        create_home: false
      - { name: yellow, state: absent, remove: true, force: true }
      - name: root
        password: '!*'
        password_lock: true
      - name: eddie   # EndeavourOS admin
        group: eddie
        groups: rfkill,sys,wheel
      - name: archie  # Arch Linux admin
        group: archie
        groups: users,wheel
      - name: debbie  # Debian admin
        group: debbie
        groups: audio,bluetooth,cdrom,dip,floppy,lpadmin,netdev,plugdev,scanner,users,video
      - name: pi      # Raspberry Pi OS admin
        group: pi
        groups: adm,audio,cdrom,dialout,games,gpio,i2c,input,netdev,plugdev,spi,sudo,users,video
      - name: booboo  # Ubuntu admin
        group: booboo
        groups: adm,cdrom,dialout,dip,fax,floppy,lpadmin,plugdev,sambashare,tape,users,video

    delta_authorized_keys:
      - user: blue
        key: ssh-ed25519 AAAAB3NzaC1yc2EAAAADAQ...  # SSH public key
        exclusive: true
      - user: red
        key: ssh-rsa AAAAB3NzaC1yc2EAAAADAQ...  # SSH public key
        state: absent

    delta_sudoers:
      - path: /etc/sudoers.d/red
        block: |
          Defaults:red !requiretty
          red ALL=(ALL) NOPASSWD:ALL
        create: true
        mode: '0440'
        validate: visudo --quiet --check --file=%s
      - path: /etc/sudoers.d/green
        block: |
          green ALL=(ALL) ALL
        create: true
        mode: '0440'
        validate: visudo -q -c -f %s
      - path: /etc/sudoers.d/blue
        block: |
          blue ALL=(ALL:ALL) ALL
        create: true
        mode: '0440'
      - path: /etc/sudoers.d/purple
        block: |
          purple ALL=(ALL:ALL) ALL
          purple ALL=NOPASSWD: /usr/bin/foo
        create: true
        mode: '0440'

CLI examples::

    # Playbook
    ansible-playbook \
        --ask-become-pass \
        --become \
        --extra-vars=@my_vars.yaml \
        --inventory=inventories/staging/ \
        --user=bob \
        start.yaml

    # Ad-hoc with SSH key
    ansible localhost \
        --args=tasks/main.yaml \
        --extra-vars=@my_vars.yaml
        --key-file=~/.ssh/id_rsa_foo \
        --module-name=import_tasks \
        --user=bob

    # Ad-hoc without SSH key
    ansible all \
        --args=tasks/main.yaml \
        --extra-vars=ansible_password=armpit
        --extra-vars=@my_vars.yaml \
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
