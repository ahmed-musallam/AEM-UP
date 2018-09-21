# :rocket: AEM-UP

*Heavily modified from [AEM-Devbox](https://github.com/ObjectiveTruth/AEM-Devbox)*

AEM Author, Dispatcher and Publisher in one VM managed via `Vagrant` and provisioned via `Ansible`. *Name comes from `Vagrant up` command*

## Stack:

  - `Vagrant`
  - `Ansible`
  - `CentOs 7` vagrant image.


## Requirements

* `vagrant 2.1.5` or higher [Install Vagrant](https://www.vagrantup.com/docs/installation/)
  
* `Ansible 2.6.4` or higher  [Install Ansible](http://docs.ansible.com/ansible/intro_installation.html)

## Setup

1. Add AEM JAR, license and dispatcher module:
	
	copy your `cq-quickstart-<version>.jar`, `license.properties` and `dispatcher-apache2.4-<version>.so` to `aem_install_files` folder.

  Folder structure should look something like this:
  
  ```
  .
  ├── LICENSE
  ├── README.md
  ├── Vagrantfile
  ├── aem_install_files
  │   ├── cq-quickstart-6.4.0.jar
  │   ├── dispatcher-apache2.4-4.2.3.so
  │   ├── license.properties
  │   └── resources
  │       ├── dispatcher-template
  │       │   ├── 00-dispatcher.conf
  │       │   ├── dispatcher.any
  │       │   └── dispatcher.conf
  │       └── services
  │           ├── aem-author.service
  │           └── aem-publish.service
  └── playbook.yml
  ```

2. open `playbook.yml` and edit the variables `aem_jar_name` and `dispatcher_module_name` (documentation in the file)

3. run `vagrant up` from project root. 
 > Might take a while. 
 >
 > (about 5 minutes on my MacBook Pro 2015 and a `200Mbs` connection)

4. buy me coffee? JK, just star the repo :)


## Dispatcher Configuration
*Dispatcher will be installed with default configs.*

### Editing dispatcher configs
  - Edit `dispatcher.any` at path:
	`aem_install_files/resources/dispatcher-template/dispatcher.any`
  - Edit dispatcher `conf` at path:
    `aem_install_files/resources/dispatcher-template/00-dispatcher.conf`

### applying dispatcher changes

Once donw with your changes, you can apply them with the provision command

```sh
ANSIBLE_ARGS='--tags "aem_dispatcher"' vagrant provision
```

## Port Forwarding

> see entries starting with `config.vm.network "forwarded_port"` in `vagrantfile`

| Instance   | Port on *host* | Port on guest (CentOs VM) |
|------------|----------------|---------------------------|
| Author     | 4602           | 4602                      |
| Publish    | 4603           | 4603                      |
| Dispatcher | 4604           | 80                        |


## SSH into VM
Use `vagrant ssh` while on the root of this project directory.
This allows you to SSH with the key vagrant generated when it setup the VM.

## Options

### JVM/AEM parameters
  
  You can set Java and AEM params in `Environment directive` in `/resources/services/aem-author.service` and `/resources/services/aem-publish.service`


### Ansible extra arguments
  You can pass extra arguments to ansible as part of the `vagrant provision` command.
  for example: 
  
  ```
  ANSIBLE_ARGS='--tags "aem_dispatcher"' vagrant provision
  ```
  
  passes `--tags "aem_dispatcher"` to ansible, to run tasks with tag `aem_dispatcher`
  
  