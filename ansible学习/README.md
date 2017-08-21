说明
===
	本目录中的文件，介绍了ansible自动化运维工具的使用和playbooks文件的编写。
	
[链接](http://lixcto.blog.51cto.com/4834175/1431659 "参考文档URL")
	
1、安装ansible
---
	mo@mo:~$ sudo apt-get install ansible -y

2、Inventory(hosts)
---	
	cat /etc/ansible/hosts 
	     [alltest:children]		#alltest组包含salt、leihuo
	     salt
	     leihuo
	     
	     [salt]
	     salt-master  ansible_ssh_user=lixc ansible_ssh_pass=123456
	     
	     10.240.162.112  ansible_connection=paramiko
	     
	     [leihuo]
	     lixc ansible_ssh_host=192.168.131.203 ansible_ssh_port=21100 
	     10.240.162.11[1:9]:22
