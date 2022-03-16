# ansible


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
