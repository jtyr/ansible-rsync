rsync
=====

Ansible role which helps to rsync remote directory via SSH.

The configuration of the role is done in such way that it should not be
necessary to change the role for any kind of configuration. All can be
done either by changing role parameters or by declaring completely new
configuration as a variable. That makes this role absolutely
universal. See the examples below for more details.

Please report any issues or send PR.


Usage
-----

```
- name: Example of basic usage of this role
  hosts: all
  vars:
    # Remote hostname
    rsync_src_host: 192.168.15.10

    # Remote user
    rsync_src_user: syncuser

    # SSH private key
    rsync_ssh_priv_key: "{{ lookup('file', 'id_rsa') }}"

    # Remote directory to rsync
    rsync_src_dir: /var/www/clamavdb/db/
  roles:
    - rsync

- name: Example of using the role multiple times
  hosts: all
  vars:
    # Remote hostname
    rsync_src_host: 192.168.15.10

    # Remote user
    rsync_src_user: syncuser

    # SSH private key
    rsync_ssh_priv_key: "{{ lookup('file', 'id_rsa') }}"
  roles:
    # First directory to rsync
    - role: rsync
      vars:
        rsync_src_dir: /var/www/clamavdb/db/
    # Second directory to rsync
    - role: rsync
      vars:
        rsync_src_dir: /var/lib/pulp/
```


Role variables
--------------

```
# Package to be installed (explicit version can be specified here)
rsync_pkg: rsync

# Remote hostname
rsync_src_host: null

# Remote user
rsync_src_user: null

# Remote directory
rsync_src_dir: null

# Destination parent directory
rsync_dst_dir: "{{ rsync_src_dir }}"

# Destination parent directory mode
rsync_dst_dir_mode: 0755

# Create symlinks between the local src and dst dirs if they are different
rsync_create_symlink: yes

# SSH private key
rsync_ssh_priv_key: null

# Custom rsync options
rsync_cmd__custom: ""

# SSH private key location
rsync_ssh_priv_key_dest: /root/.ssh/rsync.key

# Default SSH options
rsync_ssh_opts__default:
  - "-l {{ rsync_src_user }}"
  - "-i {{ rsync_ssh_priv_key_dest }}"

# Custom SSH options
rsync_ssh_opts__custom: []

# Final SSH options
rsync_ssh_opts: "{{
  rsync_ssh_opts__default +
  rsync_ssh_opts__custom }}"

# Custom rsync options
rsync_cmd_opts__default:
  - "--recursive"
  - "--links"
  - "--perms"
  - "--delete"

# Custom rsync options
rsync_cmd_opts__custom: []

# Final rsync options
rsync_cmd_opts: "{{
  rsync_cmd_opts__default +
  rsync_cmd_opts__custom }}"

# rsync command
rsync_cmd: >-
  rsync \
    {{ rsync_cmd_opts | join(' ') }} \
    --rsh 'ssh {{ rsync_ssh_opts | join(' ') }}' \
    {{ rsync_cmd__custom }} \
    {{ rsync_src_host }}:{{ rsync_src_dir }} {{ rsync_dst_dir }}
```


License
-------

MIT


Author
------

Jiri Tyr
