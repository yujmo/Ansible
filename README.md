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
	        salt-master  ansible_ssh_user=lixc ansible_ssh_pass=123456 	#设置主机的默认连接用户、密码
	        10.240.162.112  ansible_connection=paramiko 	#设置ssh的连接方式，默认openssh
	     
	     	[leihuo]
	        lixc ansible_ssh_host=192.168.131.203 ansible_ssh_port=21100 
	        10.240.162.11[1:9]:22
	     
3、vars
---
	变量：主机变量、组变量
	例子1：	
	cat /etc/ansible/hosts 
		[alltest:children]
		salt
	        leihuo
	    
	        [salt]
	        salt-master  salt-port=4505 mysql-port=3306
	        10.240.162.112  salt-path=/usr/bin/salt-call
	     
	        [leihuo]
	        lixc ansible_ssh_host=192.168.131.203 ansible_ssh_port=21100 
	        10.240.162.11[1:9]:22
	     
	        [alltest:vars]		#组变量
	        ls-path=/bin/ls
	    	liss=lisisi
	例子2：	
	cat /etc/ansible/host_vars/salt-master
		---
		salt-port: 4505
		mysql-port: 3306

4、目标匹配
---
	匹配所有主机
		*或者all
	匹配多个组
		salt:leihuo
	在salt这个组里，但不能在leihuo这个组里的主机
		salt:!leihuo
	取两个组的交集
		salt:&leihuo
	排除某一主机
		ansible-playbook site.yaml --limit salt-msater
	当然也可以用正则，在/etc/ansible/hosts里面去定义。如
		~salt(master|minion)\.li*\.com


5、命令行模块
---
	命令行三剑客：command、shell、raw
	
	#默认command，但是commmand不能用shell的一些特性，比如shell中的通配符*
	mo@mo:~$ grep -n "module_name" /etc/ansible/ansible.cfg 
		 95:#module_name = command
	
	#raw是在新的机器上没有python环境并且shell和command不能使用的情况下，只能使用raw

	例子：
		#系统命令
		1. ansible  10.240.162.250 -m raw -a 'aptitude -y install python' -u root -k >/dev/null
		2. ansible  10.240.162.250 -m raw -a 'ls /tmp/*' -u root -k
		3. ansible salt-master -m shell -a 'echo $HOME'
		4. ansible salt-master -m shell -a 'ls -l /etc/sudoers*'
		#文件操作
		1. ansible salt-master -m copy -a 'src=/etc/sudoers dest=/etc/sudoers owner=root group=root mode=440 backup=yes' -s
 		2. ansible salt-master -m file -a 'src=/etc/sudoers dest=/tmp/sudoers mode=440 owner=lixc group=lixc state=link'
		3. ansible salt-master -m file -a 'dest=/tmp/a/b/c  owner=lixc group=lixc mode=755 state=directory'
