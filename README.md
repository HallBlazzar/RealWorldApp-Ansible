# This is Ansible playbook to deploy RealWorldAppContainer #

## Requirement ##

* CentOS 7
* Ansible(2.4.2.0)

## How to deploy ##

1. Clone the repository

    `$git clone https://github.com/HallBlazzar/RealWorldApp-Ansible.git`

2. Add connection information in `${path_to_project_directory}/hosts`

3. Modify options in `${path_to_project_directory}/vars/env.yml` and `${path_to_project_directory}/vars/source.yml`

    * `${path_to_project_directory}/vars/env.yml`
    
        |Option|Description|Default Value|
        |--|--|--|
        ||||
    
    * `${path_to_project_directory}/vars/source.yml`

4. Execute playbook

    `$ cd ${path_to_project_directory}`

    `$ ansible-palybook -i hosts main.yml`
    
## Development ##
