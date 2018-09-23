# :rocket: AEM-UP

*Inspired by [AEM-Devbox](https://github.com/ObjectiveTruth/AEM-Devbox)*

AEM Author, Publisher and Dispatcher in one VM With a logging server\*. managed via `Vagrant` and provisioned via `Ansible`.  *Name comes from `Vagrant up` command*

> *This VM runs a [tailon](https://github.com/gvalkov/tailon) server to show you logs in browser! no SSH necessary!

- Table of Contents
  * [Stack:](#stack)
  * [Requirements](#requirements)
  * [Setup](#setup)
  * [Configuration](#configuration)
    + [Port forwarding](#port-forwarding)
    + [Runmodes](#runmodes)
    + [Dispatcher Configuration](#dispatcher-configuration)
        * [Editing dispatcher configs](#editing-dispatcher-configs)
        * [applying dispatcher changes](#applying-dispatcher-changes)
    + [Increase VM memory and CPU cores](#increase-vm-memory-and-cpu-cores)
    + [JVM/AEM parameters](#jvm-aem-parameters)
    + [Ansible extra arguments (Mac/Linux only)](#ansible-extra-arguments--mac-linux-only-)
  * [Default Ports](#default-ports)
  * [SSH into VM](#ssh-into-vm)
  * [Logs](#logs)
  * [VM script (Mac/Linux* only)](#vm-script--mac-linux--only-)

<small><i><a href='http://ecotrust-canada.github.io/markdown-toc/'>Table of contents generated with markdown-toc</a></i></small>


## Stack

  - `Vagrant`
  - `Ansible`
  - `CentOs 7` vagrant image.


## Requirements

- `vagrant 2.1.5` or higher [Install Vagrant](https://www.vagrantup.com/docs/installation/)
- `Ansible 2.6.4` or higher  [Install Ansible](http://docs.ansible.com/ansible/intro_installation.html)
- AEM JAR and license.
- Latest Dispatcher module*. 

> *Download from [PackageShare](https://www.adobeaemcloud.com/content/companies/public/adobe/dispatcher/dispatcher.html). choose `Linux x86 64bit` for example: `dispatcher-apache2.4-linux-x86-64-4.2.3.tar.gz`

## Setup

1. **Add AEM JAR, license and dispatcher module:**
	
	copy your `cq-quickstart-<version>.jar`, `license.properties` and `dispatcher-apache2.4-<version>.so` to `user-provided` folder.
	> Pay close attention to step 2

  Folder structure should look something like this:
  
  ```
	.
	├── LICENSE.md
	├── README.md
	├── Vagrantfile
	├── binaries
	│   └── tailon
	│       ├── LICENSE
	│       ├── README.md
	│       └── tailon
	├── dispatcher
	│   ├── 00-dispatcher.conf
	│   ├── dispatcher.any
	│   └── dispatcher.conf
	├── playbook.yml
	├── services
	│   ├── aem-author.service
	│   ├── aem-publish.service
	│   └── tailon.service
	├── user-provided
	│   ├── cq-quickstart-6.4.0.jar
	│   ├── dispatcher-apache2.4-4.2.3.so
	│   └── license.properties
└── vm
  ```

2. **Tell Ansible where your file names**
	
	open `playbook.yml` and edit the variables `aem_jar_name` and `dispatcher_module_name`** (documentation in the file)

3. **Run VM**

	On Mac? use `./vm up`. see the VM script below.
	
	Not on Mac? run `vagrant up`
	
	> This VM binds no non-standard ports (46##), see [Default Ports](#default-ports) below. This can be changed: read [Configuration](#configuration) section below.
	
	> HELP: If you have experience converting bash scripts to windows, I could use the help to convert `vm` script.

4. **buy me coffee? JK, just star the repo :)**

	> NOTE: you will need to setup replication agents and configs as needed. This setup only installs AEM.


## Configuration

### Port forwarding
> ie. Changing `localhost` ports

You can do this in the `Vagrantfile`. There is a `port forwarding`. You may se entries like:

```ruby
# Ports Forwarding  
config.vm.network "forwarded_port", guest: 4602, host: 4602
config.vm.network "forwarded_port", guest: 4603, host: 4603
config.vm.network "forwarded_port", guest: 80, host: 4604
config.vm.network "forwarded_port", guest: 8080, host: 4605
``` 

leave `guest` ports untouched, and change `host` ports to your heart's content!

### Runmodes
in `playbook.yml` you can change values for `aem_publish_runmodes` and `aem_author_runmodes`

### Dispatcher Configuration
*Dispatcher will be installed with default configs.*

##### Editing dispatcher configs
  - Edit `dispatcher.any` at path:
    `dispatcher/dispatcher.any`
  - Edit dispatcher `conf` at path:
    `dospatcher/00-dispatcher.conf`

##### applying dispatcher changes
...after the VM has been created.

Once done with your changes, you can apply them with the provision command:

```sh
ANSIBLE_ARGS='--tags "aem_dispatcher"' vagrant provision
```
> this wont work on windows, you'd have to run `vagrant provision` to re-provision the whole VM. Unless you know how to pass env variables :)

### Increase VM memory and CPU cores

in `vagrantfile` change `vb.memory` and `vb.cpu`

### JVM/AEM parameters
  
  You can set Java and AEM params in `Environment` directive in `services/aem-author.service` and `services/aem-publish.service`


### Ansible extra arguments (Mac/Linux only)
  You can pass extra arguments to Ansible as part of the `vagrant provision` command by setting the params to the environment variable `ANSIBLE_ARGS`.
  
  > HELP: need to know how to do this on windows.
  
  for example: 
  
  ```
  ANSIBLE_ARGS='--tags "aem_dispatcher"' vagrant provision
  ```
  
  passes `--tags "aem_dispatcher"` to ansible, to run tasks with tag `aem_dispatcher`


## Default Ports

| Instance   | Port on *host* | Port on guest (CentOs VM) |
|------------|----------------|---------------------------|
| Author     | 4602           | 4602                      |
| Publish    | 4603           | 4603                      |
| Dispatcher | 4604           | 80                        |
| Tailon     | 4605           | 8080                      |

## SSH into VM
Use `vagrant ssh` while on the root of this project directory.
This allows you to SSH with the key vagrant generated when it setup the VM.

## Logs

Go to `localhost:4605` to see logs reported by [tailon](https://github.com/gvalkov/tailon)

you can also SSH into VM as mentioned above to see logs.

| Instance   | Log Location in the VM                          |
|------------|-------------------------------------------------|
| Author     | `/home/vagrant/aem-author/crx_quickstart/logs`  |
| Publish    | `/home/vagrant/aem-publish/crx_quickstart/logs` |
| Dispatcher | `/etc/httpd/logs`                               |

## VM script (Mac/Linux* only)

> *Must support bash

run `./vm help` or look into the `vm` file, I added a LOT of documentation :).


  
  