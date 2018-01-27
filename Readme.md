# Running Ansible inside Docker container

### The command for running ansible-playbook from inside the container, e.g.:

```
docker run --rm -it -v $(pwd):/ansible/playbooks \
    walokra/ansible-playbook site.yml
```

If Ansible is interacting with external machines, you’ll need to mount an SSH key pair for the duration of the play:

```
docker run --rm -it \
    -v ~/.ssh/id_rsa:/root/.ssh/id_rsa \
    -v ~/.ssh/id_rsa.pub:/root/.ssh/id_rsa.pub \
    -v $(pwd):/ansible/playbooks \
    walokra/ansible-playbook site.yml
```

To make things easier you can use shell script named ansible_helper that wraps a Docker image containing Ansible:

```
#!/usr/bin/env bash
docker run --rm -it \
  -v ~/.ssh/id_rsa:/root/.ssh/id_rsa \
  -v ~/.ssh/id_rsa.pub:/root/.ssh/id_rsa.pub \
  -v $(pwd):/ansible_playbooks \
  -v /var/log/ansible/ansible.log \
  walokra/ansible-playbook "$@"
```

Point the above script to any inventory file so that you can execute any Ansible command on any host, e.g.

```./ansible_helper play playbooks/deploy.yml -i inventory/dev -e 'some_var=some_value'```