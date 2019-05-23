# This is Ansible playbook to deploy RealWorldAppContainer #

## Requirement ##

* CentOS 7
* Ansible(2.4.2.0)

## How to deploy ##

1. Clone the repository

    `$git clone https://github.com/HallBlazzar/RealWorldApp-Ansible.git`

2. Add connection information of target host in `${path_to_project_directory}/hosts`

3. Modify options in `${path_to_project_directory}/vars/env.yml` and `${path_to_project_directory}/vars/source.yml`

    * `${path_to_project_directory}/vars/env.yml`

        | Option | Description | Default Value |
        |--|--|--|
        | database_name | database name for storing data for backend | demo |
        | database_user | user name to access postgresql | demo |
        | database_host | database host name, only modify it if you don't want to store data in postgresql database provided by `RealWordAppContainer` | database |
        | database_port | port number of database service , only modify it if you don't want to store data in postgresql database provided by `RealWordAppContainer` | 5432 |
        | service_domain_name | domain name to access `RealWordAppContainer` service | demo.com |
        | wsgi_processes | number of wsgi processes of backend, modify it for performance requirement | 10 |
        | nginx_worker_process | number of nginx worker processes of backend, modify it for performance requirement | 1 |
        | nginx_keep_alive_timeout | connection timeout of nginx of backend, modify it for performance requirement | 65 |
        | nginx_client_max_body_size | max request body size of nginx of backend, modify it for performance requirement | 75M |

    * `${path_to_project_directory}/vars/source.yml`

        | Option | Description | Default Value |
        |--|--|--|
        | docker_source | URL of official `docker` repository of `yum`. Effects which version of `docker` will be deployed.  | https://download.docker.com/linux/centos/docker-ce.repo |
        | docker_compose_source | URL of official `docker-compose`. Effects which version of `docker-compose` will be deployed | https://github.com/docker/compose/releases/download/1.24.0/docker-compose-Linux-x86_64 |
        | real_world_app_container_source | Git repository of `RealWorldAppContainer` | https://github.com/HallBlazzar/RealWordAppContainer.git |
        | frontend_source | Git repository of `Vue.js` frontend of `RealWorldAppContainer` | https://github.com/gothinkster/vue-realworld-example-app.git |
        | backend_source | Git repository of `Django` backend of `RealWorldAppContainer` | https://github.com/gothinkster/django-realworld-example-app.git |
        | project_dir | Full path of where `RealWorldAppContainer` will be deployed in target host | /opt/real_world_app_container |

4. Execute playbook

    `$ cd ${path_to_project_directory}`

    `$ ansible-playbook -i hosts main.yml`

## Development ##

1. Prepare environment.

    This project is developed and tested based on `CentOS7` environment defined by `Vagrantfile` below:

    ```
    Vagrant.configure("2") do |config|
      config.vm.box = "centos/7"

      hosts = {
        "master" => { :ip => "10.11.0.10" },
        "slave" => { :ip => "10.11.0.11" }
      }

      hosts.each_with_index do |(hostname, info), index|
        config.vm.define "#(hostname)" do | host |
          host.vm.network "private_network", ip:"#{info[:ip]}"
          host.vm.provider "virtualbox" do |v|
            v.memory = "1024"
            v.cpus = "2"
          end
        end
      end
    end
    ```

    And constructed by:

    `$ vagrant up`

    2 VMs constructed for development, `master` and `slave`, respectively. In `master`, ansible is installed for deploying project through:

    `# yum install ansible`

    `slave` is used to be deployed `RealWorldAppContainer`.

2. Set connection information.

    On shell of host machine, execute:

    `$ vagrant ssh-config slave`

    to get ssh-key file path, then create `private-key` file and copy ssh-key in it under project directory. On important thing is to modify permission of `private-key` to prevent `ssh` command cannot read it:

    `$ chmod 600 ${path_to_project_directory}/private_key`

    As a result, in `master` machine, `hosts` file of `RealWorldApp-Ansible` is defined as below:

    ```
    [target]
    target ansible_ssh_host=10.11.0.111 ansible_ssh_private_key_file=./private_key
    ```
3. Deploy

    Try

    `ansible all -i hosts -m ping`

    to check connectivity and ssh accessibility between master and slave. If failed, carefully re-check environment in step 1 and 2. Then execute:

    `$ ansible-playbook -i hosts main.yml`

    under project directory.
