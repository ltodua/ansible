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


