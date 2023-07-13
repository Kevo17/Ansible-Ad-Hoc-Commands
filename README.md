<h1>Ansible Ad-Hoc Commands</h1>


<h2>Description</h2>
This lab focuses on Ansible's Ad-Hoc Commands feature, which allows for quick and efficient management and automation of remote systems. Ansible Ad-Hoc Commands provide a powerful way to perform common tasks and execute ad-hoc operations across multiple servers without the need for complex playbooks or configurations.
<br />


<h2>Languages and Utilities Used</h2>

- <b>Ansible</b>

<h2>Environments Used </h2>

- <b>Windows 11</b>
- <b>Linux</b> 

<h2>Program walk-through:</h2>

First, we need to install the Ansible program: <br/>
- pip3 install ansible==6.4.0<br/>
 
<p align="center">
<img src="https://i.imgur.com/DUwt3ZK.png" height="80%" width="80%" alt="Disk Sanitization Step"/>
</p>

<br />
<br />

The headings in the bracket are a group name that used to defines our hosts. So let’s create the inventory file for Ansible using the following command: <br/>
- cat > inventory.ini <<EOL

[devsecops]
devsecops-box-rcgsg0ei

[sandbox]
sandbox-rcgsg0ei

[prod]
prod-rcgsg0ei

EOL<br/>
 
<p align="center">
<img src="https://i.imgur.com/L7hAXn8.png" height="80%" width="80%" alt="Disk Sanitization Step"/>
</p>

<br />
<br />

To see which hosts in our inventory matches a supplied group name, let’s try the following command: <br/>
- ansible -i inventory.ini prod --list-hosts<br/>
 
<p align="center">
<img src="https://i.imgur.com/VO7g6Od.png" height="80%" width="80%" alt="Disk Sanitization Step"/>
</p>

<br />
<br />

You can change the prod value to another group name like sandbox or devsecops to see if there is a host match. In case there is no host match, the output looks like below for a group named gitlab that does not exist in our inventory file.: <br/>
- ansible -i inventory.ini gitlab --list-hosts<br/>
 
<p align="center">
<img src="https://i.imgur.com/0cyu8HT.png" height="80%" width="80%" alt="Disk Sanitization Step"/>
</p>

<br />
<br />

You can see if there is any default configuration through the following command: <br/>
- ansible --version<br/>
 
<p align="center">
<img src="https://i.imgur.com/Q32OsHa.png" height="80%" width="80%" alt="Disk Sanitization Step"/>
</p>

<br />
<br />

Because we don’t have an ansible.cfg in our machine, we can create it manually by doing: <br/>
- mkdir /etc/ansible/
- cat > /etc/ansible/ansible.cfg <<EOF
[defaults]
stdout_callback = yaml
deprecation_warnings = False
host_key_checking = False
retry_files_enabled = False
inventory = /inventory.ini
EOF<br/>
 
<p align="center">
<img src="https://i.imgur.com/1PzBNzu.png" height="80%" width="80%" alt="Disk Sanitization Step"/>
</p>

<br />
<br />

For example, we want to use ping module through ad-hoc command. But before we do that, we will have to ensure the SSH’s yes/no prompt is not shown while running the ansible commands, so let’s use the ssh-keyscan to capture the key signatures beforehand: <br/>
- ssh-keyscan -t rsa devsecops-box-rcgsg0ei sandbox-rcgsg0ei prod-rcgsg0ei >> ~/.ssh/known_hosts<br/>
 
<p align="center">
<img src="https://i.imgur.com/KxiRPiY.png" height="80%" width="80%" alt="Disk Sanitization Step"/>
</p>

<br />
<br />

Then execute the ansible command: <br/>
- ansible -i inventory.ini all -m ping<br/>
 
<p align="center">
<img src="https://i.imgur.com/fvFd0Xd.png" height="80%" width="80%" alt="Disk Sanitization Step"/>
</p>

<br />
<br />

As you can see the command output above, all our hosts (line number 1, 8 and 15) are connected to Ansible. Next, use the shell module of Ansible to run the hostname command on all machines: <br/>
- ansible -i inventory.ini all -m shell -a "hostname"<br/>
 
<p align="center">
<img src="https://i.imgur.com/D1xTk6v.png" height="80%" width="80%" alt="Disk Sanitization Step"/>
</p>

<br />
<br />

We can use another module to install a package inside the remote host: <br/>
- ansible -i inventory.ini all -m apt -a "name=ntp"<br/>
 
<p align="center">
<img src="https://i.imgur.com/V9Y7tL3.png" height="80%" width="80%" alt="Disk Sanitization Step"/>
</p>

<br />
<br />

To find the available modules, you can check out this link or use the local help command-line tool: <br/>
- ansible-doc -l | egrep "add_host|amazon.aws.aws"<br/>
 
<p align="center">
<img src="https://i.imgur.com/TkA9FtJ.png" height="80%" width="80%" alt="Disk Sanitization Step"/>
</p>

<br />
<br />

Use the ansible-doc command to see help examples and find a module that can send a file from DevSecOps-Box to remote machines: <br/>
- ansible-doc -h<br/>
 
<p align="center">
<img src="https://i.imgur.com/NZTVhTz.png" height="80%" width="80%" alt="Disk Sanitization Step"/>
</p>

<br />
<br />

Create a file with some content, let the file name be notes at the location /root. Also using an ansible ah-hoc command, copy the file /root/notes into all remote machines (sandbox and production) to the destination directory /root: <br/>
- echo "test" > /root/notes<br/>
- ansible -i inventory.ini all -m copy -a "src=/root/notes dest=/root"

 
<p align="center">
<img src="https://i.imgur.com/K2FhMa4.png" height="80%" width="80%" alt="Disk Sanitization Step"/>
</p>

<br />
<br />


<!--
 ```diff
- text in red
+ text in green
! text in orange
# text in gray
@@ text in purple (and bold)@@
```
--!>
