# ansible


## ssh for connection
to connect to other servers first generate sshkey-s and copy it to servers

ssh-keygen command will create the sshkey
ssh-copy-id command will copy it

ansible commands
1.

`ansible all --key-file ~/.ssh/ssh-key-name -i inventory -m ping`

we can shorten this file by creating ansible.cfg file in the working directory.

in the newly created ansible.cfg file we write:

[defaults]
inventory = inventory #name of the inventory file where all our host are located
private_key_file = ~/.ssh/ssh-key-name #name of the ssh-key file that we gerenared and copyed to our host server(s)


## simple ad-hoc commands

after creating the andible.cfg file our first command has shortened so it goes like this:
`ansible all -m ping`

ansible also can guther facts about host mashines or VM-s. every little detail it has per say.
command:
`ansible all -m gather_facts` also we can limit the fact gathering of the inventory hosts by adding `--limit $host-ip` to the command `ad-hoc` in ansible terminology


we cant execute sudo task without assigning new parameter to ansible adhoc command.
for sudo access we have to add `--become --ask-become-pass` wchich will prompt us with hosts sudo password and then execute the command.
for example, if we want to update apt of the host. we can run ad-hoc as such:
`ansible all -m apt -a update_cache=true --become --ask-become-pass`
-m means add module ex: apt
-a means add argument ex: update_cache=true
--become add sudo access
--ask-become-pass add sudo password

if hosts have same sudo password this this command will work. else ... (i'll add later on)

we can add multipe arguments to the module by puting the argument in double quote ("") and put stace between arguments

ex: 
`ansible all -m apt -a "name=snapd state=latest" --become --ask-become-pass`

## playbooks

lets talk about ansible playbooks.
ansible playbooks are the main way or to run multiple tasks or anything really, and execute ansible commands. for exampe: in the work directory i have created install_apache2.yml file to write and execute ansible playbooks.

ansible playbooks are written in YML format.
install_apache2.yml:

` --- ` playbooks starts with three dash-es in order to let the sistem know that this is yml file
under the dashes ` - hosts: all ` to indentifie the hosts and execute commands on them this is equal to the ad-hoc command `ansible all`
we push enter and under the first letter of the `hosts` we write sudo priviladge parameter `become=true` this is equal to `--become`

after this we can write tasks. what playbook must execute.
under the first letter of parameter `--become` we write `tasks:` then press enter.
under the first letter of `tasks: ` we write `- name: ` this is the begining of the task. every task must be written in this manner and be properly named.
`- name: install apache2 package` this is the name of the task
under the first letter of `- name` we write module name. so in our case `apt:` then enter.
after enter we must make sure that between apt and next word space diference are 2 spaces and write name of the package we want to install, so `name: apache2`. we save the file and execute it:

`ansible-playbook --ask-become-pass install_apache2.yml`
ansible-palybook is the command with we execute playbooks
-ask-become-pass means that we have to include password of sudo in order to make sudo changes
install_apache2.yml is the name of the playbook

you can add as many tasks in the playbook as you want.

## when

when command in `ansible-playbook`.

`when` is similar to `if` statement. when we declear when we can assign condition to the each play. when command to work we must `gather_facts` about hosts so execute the play if when command is true or false. we also can include our own conditons to when command which we'll have to pass as argument to `ansible-playbook` command up on execution. 

okaaay. well ansible is really cool

i learned that ansible has General package manager module called `package` which i can include in the playbook.

also you can declear your own variables. 
ex: `"{{ name }}"` like this which you'll declear in inventory file and assign them individual values if neccesary for each host.


in inventory file we can devide or group hosts together. we can assign each group some kind of name. we can use this name in the playbook to do certain tasks with certain groups or hosts whatever it the purpose of them.


## tags

to test plays individualy or so ansible has tags concept. where we can give any play any or as many tags as we want. we can run playbook with multipe or single tag.

to assign tags to plays we siply have to add `tags: $name,#name2` parameter, if we want to have multiple tags in one play we should only separate it with coma no spaces.

in order to run plays with tags first we have to list them. to list we have to execute command:

`ansible-playbook --list-tags name_of_playbook.yml` this will give us the names of all the tags we can execute within playbook.

to execute individual tags. run:

`ansible-playbook -K --tags $tag_name name_of_playbook.yml` 

to execute multiple tags. run:

`ansible-playbook -K --tags "$tag1,tag2,tag2" name_of_playbook.yml`

## managing files

how to add files to hosts server ...

created html page by name default_site.html which contains html boilerplate setup and siple header tag. this html file was copyed to the server as index.html file

how to install binary package on hosts server ...

in order to install binary applications on hosts first we must create play for installing unzip app and then create play with `unarchive` module of ansible.

i have added comments about services and few ansible modules. at `site.yml` file

more descriptive info later...

`registar:` registar saves ansible play output into the variable. variable can be anystring name what we can come up with

`lineinfile` module is for changing line of specific file which we must include into the play under lineinfile module. plus we need regular expretion to find specific line which we want to change, at last we have to include the line itself to replace previous line with


## managing services and creating users

`services` how can we use them in the playbook. with help of the `service` module we can start, stop, restart, status all the basic `service` commands we can execute them all.

to manage the ansible hosts we must create ansible user and assign it sudoers priviladges. for more details see `bootstrap.yml` file

bootstrap.yml file will update the hot repos, will create user, assign user workstation ssh key and add the name od the user in sudoers file.

this step is done for preperation of the hosts in order to execute anbile configuration smoothly.

if step is done successfully we can even you basic playbooks without asking for sudo password.


## roles

to use roles. we must declear role property at the beggining of the playbook instead of tasks property. and we have to name them as we want.
after declearing roles we have to create folders for each role we have created. role described as a thing the playbook must do. it helps us refactor our yml code and give them little more style.
so I created three roles
`base` , `web_servers`, `workstation` after creating the folders, each folder must include tasks folder. the task folder is the where our playbooks ( taskbooks ) will be located. and name of the yml file will be `main.yml`

`base` role task folder includes play for managing ssh-key for hosts
`web_server` role task folder includes all the installation plays of the apache servers
`workstation` role task folder includes play for installing archive file for the terraform binary application

in web_server role folder we also have files folder, this is folder for resources that web_server play needs to execute. 

with this modification site.yml only has all the hosts servers repo update and decleration of roles.

## Hosts and Handlers

