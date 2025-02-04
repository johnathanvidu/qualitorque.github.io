---
sidebar_position: 24
title: The Ansible Grain
---

The Ansible grain is Torque’s native support for orchestrating the execution of Ansible playbooks as part of a Torque blueprint. The referenced playbook can rely on vars or inventory-hosts that are dynamically provided by Torque, and then utilize them to perform configuration management, updates, a health check or any other flow that is executable from Ansible. 

Note: an Ansible grain does not provide grain outputs. 

### Tools and technologies

The following tools and technologies are installed out of the box on our agents in the Kubernetes pods and can be used when writing grain scripts (pre/post, etc.):

* dotnet
* git
* python3
* pip3
* ansible
* openssh-client

### source
The source section of an Ansible grain provides Torque with the information on where the Ansible playbook is stored and should be retrieved from. This could be either a direct source URL to an Ansible playbook YAML file, or it can be a reference from a Torque-connected git repository. 

__Example - direct:__

```yaml 
My_Ansible_Grain:
  depends-on: Ubuntu_VM
  kind: ansible
  spec:
    source:
      path: https://github.com/MyOrg/my-repo//ansible/install_httpd_rhel.yaml
```

__Example - repository:__

```yaml 
My_Ansible_Grain:
  kind: ansible
  spec:
    source:
      store: assets
      path: assets/ansible/install_apache2_webgame_ubuntu.yaml
```

### agent
Please see [the grain agent](/blueprint-designer-guide/blueprints/blueprints-yaml-structure#host) for more details.

### Inventory-file

Inventory file is a special grain section unique to the ansible grain, that allows you to provide in a YAML structured format, the content of the Ansible inventory file that will be generated for the Ansible playbook to use. For a deep understanding of the format of this file, please see Ansible’s official documentation at https://docs.ansible.com/ansible/latest/inventory_guide/intro_inventory.html#inventory-basics-formats-hosts-and-groups.
The general standard structure for such a file is as follows:

```yaml 
host_group:
  hosts:
    host1:
      host1_var: host1_var_value
    host2:
      host2_var: host1_var_value
  vars:
    group_var: group_var_value
```

Depending on the Ansible packages used in the playbook, these host and group variables may be used automatically by the package step (in which case, they must be provided in a specific name, for example an “ansible_become: yes/no” group var will determine if the actions in the playbook need to be run as a different user from the host connection info.). 
Variables not defined on a host will automatically default to the values set in the vars section of the host_group. For example, the below playbook:

```yaml 
---
- gather_facts: no
  hosts: all
  tasks:
  - name: Print Hello World
    debug: 
      msg: "Hello World, my name is {{ person_name}}"
```

with the below inventory file:
```yaml 
      inventory-file:
        all:
          hosts:
            host1:
              person_name: John
            host2:          
          vars:
            person_name: Doe
```

will result in this output

```yaml 
TASK [Print Hello World] *******************************************************
ok: [host1] => {
    "msg": "Hello World, my name is John"
}
ok: [host2] => {
    "msg": "Hello World, my name is Doe"
}
```

Any section of the “inventory-file:” can contain dynamic values from dependent grains, from the blueprint’s inputs or from Torque’s parameter storage. For more information, see  [Torque Templating engine](/blueprint-designer-guide/blueprints/blueprints-yaml-structure#torque-templating-engine).
__Example:__ Below is an example of a grains section of a blueprint, containing an Ansible grain:

```yaml
 grains:
  hello_world_ansible:
    kind: ansible
    spec:
      source:
        store: assets
        path: ansible/hello_world.yaml
      agent:
        name: my-torque-agent
      inventory-file:
        all:
          hosts:
            host1:
              person_name: John
            host2:          
          vars:
            person_name: Doe
```