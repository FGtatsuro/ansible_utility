ansible_utility
===============

Utility tasks for Ansible.

pre_tasks
---------

Pre tasks for each connection type of Ansible. In almost cases, this is used with `post_tasks`.

Now, only the implementation for Docker exists. In other connection types, nothing is done.

- Docker: Before provisioning, a container with specified container name runs.

### Usage

We can use it as follows in playbook.

```yaml
- hosts: all
  # Prevent error occurs before pre_tasks. (ex. Docker container isn't running.)
  gather_facts: no
  pre_tasks:
    - name: Do pre-task for each platform
      include: "pre_tasks/{{ ansible_connection }}.yml"
    - name: "Gather fact information on target platform."
      setup:
```

### Variables

Docker

|name|description|type|default|note|
|---|---|---|---|---|
|inventory_hostname|Container with this value as container name runs.|str|-|It's pre-defined Ansible from inventory file. Thus, we don't have to define it.|
|utility_docker_base_image|Container is generated from this image.|str|-|In almost cases, it's better to define in inventory.|
|utility_docker_extra_options|Container runs with this value as Docker options.|str|-|-|
|utility_docker_command_become|If the value is yes/true, container runs with sudo.|bool|false|This is needed when Docker command execution needs root privilege.|

post_tasks
----------

Post tasks for each connection type of Ansible. In almost cases, this is used with `pre_tasks`.

Now, only the implementation for Docker exists. In other connection types, nothing is done.

- Docker: After provisioning, a container with specified image name is committed.

### Usage

We can use it as follows in playbook.

```yaml
post_tasks:
  - name: Do post-task for each platform
    include: "post_tasks/{{ ansible_connection }}.yml"
```

### Variables

Docker

|name|description|type|default|note|
|---|---|---|---|---|
|inventory_hostname|Container with this value as container name is committed.|str|-|It's pre-defined Ansible from inventory file. Thus, we don't have to define it.|
|utility_docker_command_become|If the value is yes/true, docker command is executed with sudo.|bool|false|This is needed when Docker command execution needs root privilege.|
|utility_docker_commit_image|Container is committed with this value as image name.|str|-|In almost cases, it's better to define in inventory.|
|utility_docker_image_committed|If the value is no/false, container is NOT committed.|str|true|This is useful when we develop provisioning code for time-saving. Commit process takes some times.<br>In this case, it's better to use this in Ansible `--extra-vars`.|

push_image
----------

A playbook to push a Docker image to given registry.

### Usage

We can use this playbook as follows.

```bash
$ ansible-playbook docker/push_image.yml -i tests/inventory/hosts -l test_host --extra-vars="utility_docker_image=fgtatsuro/infra-bridgehead:alpine-3.3"
```

### Variables

|name|description|type|default|note|
|---|---|---|---|---|
|utility_registry_url|A repository url which a Docker image is pushed to.|localhost:5000||
|utility_docker_image|a name of a target Docker image. This is a required variable.|It isn't defined in default.||
|utility_docker_command_become|If the value is yes/true, docker command is executed with sudo.|bool|false|This is needed when Docker command execution needs root privilege.|

- If a tag `{{ utility_registry_url }}/{{ utility_docker_image }}` exists before playbook execution, it is removed in playbook execution.

pull_image
----------

A playbook to pull a Docker image to given registry.

### Usage

```bash
$ ansible-playbook docker/pull_image.yml -i tests/inventory/hosts -l test_host --extra-vars="utility_docker_image=fgtatsuro/infra-bridgehead:alpine-3.3"
```

### Variables

|name|description|type|default|note|
|---|---|---|---|---|
|utility_registry_url|A repository url which a Docker image is pushed to.|localhost:5000||
|utility_docker_image|a name of a target Docker image. This is a required variable.|It isn't defined in default.||
|utility_docker_command_become|If the value is yes/true, docker command is executed with sudo.|bool|false|This is needed when Docker command execution needs root privilege.|

- If a tag `{{ utility_registry_url }}/{{ utility_docker_image }}` exists before playbook execution, it is removed in playbook execution.
