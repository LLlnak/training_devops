Vagrant.configure(2) do |config|
	config.vm.box = "cdaf/CentOSLVM"
	config.vm.synced_folder ".", "/vagrant", disabled: true
	config.vm.provider "hyperv" do |hv|
		hv.ip_address_timeout = 300
	end
	config.vm.provision "yum", type: "shell", inline: "yum install mc -y"

$git = <<SCRIPT
yum install git -y
git init
git clone https://github.com/LLlnak/training_devops
cd training_devops
git checkout task2
SCRIPT

$ip1 = <<SCRIPT
echo "DEVICE=eth0" > /etc/sysconfig/network-scripts/ifcfg-eth0
echo "BOOTPROTO=none" >> /etc/sysconfig/network-scripts/ifcfg-eth0
echo "ONBOOT=yes" >> /etc/sysconfig/network-scripts/ifcfg-eth0
echo "IPADDR=192.168.1.201" >> /etc/sysconfig/network-scripts/ifcfg-eth0
echo "NETMASK=255.255.255.0" >> /etc/sysconfig/network-scripts/ifcfg-eth0
echo "GATEWAY=192.168.1.1" >> /etc/sysconfig/network-scripts/ifcfg-eth0
echo "DNS1=8.8.8.8" >> /etc/sysconfig/network-scripts/ifcfg-eth0
SCRIPT

$web = <<SCRIPT
yum install httpd -y
systemctl enable httpd
systemctl start httpd
firewall-cmd --permanent --zone=public --add-port=80/tcp
firewall-cmd --reload
cp /home/vagrant/training_devops/mod_jk.so /etc/httpd/modules/
echo "worker.list=lb" > /etc/httpd/conf/workers.properties
echo "worker.lb.type=lb" >> /etc/httpd/conf/workers.properties
echo "worker.lb.balance_workers=tom1, tom2" >> /etc/httpd/conf/workers.properties
echo "worker.tom1.host=192.168.1.202" >> /etc/httpd/conf/workers.properties
echo "worker.tom1.port=8009" >> /etc/httpd/conf/workers.properties
echo "worker.tom1.type=ajp13" >> /etc/httpd/conf/workers.properties
echo "worker.tom2.host=192.168.1.203" >> /etc/httpd/conf/workers.properties
echo "worker.tom2.port=8009" >> /etc/httpd/conf/workers.properties
echo "worker.tom2.type=ajp13" >> /etc/httpd/conf/workers.properties
echo "worker.list=status" >> /etc/httpd/conf/workers.properties
echo "worker.status.type=status" >> /etc/httpd/conf/workers.properties
echo "LoadModule jk_module modules/mod_jk.so" > /etc/httpd/conf/mod_jk.conf
echo "JkWorkersFile conf/workers.properties" >> /etc/httpd/conf/mod_jk.conf
echo "JkShmFile /tmp/shm" >> /etc/httpd/conf/mod_jk.conf
echo "JkLogFile logs/mod_jk.log" >> /etc/httpd/conf/mod_jk.conf
echo "JkLogLevel info" >> /etc/httpd/conf/mod_jk.conf
echo "JkMount /test* lb" >> /etc/httpd/conf/mod_jk.conf
echo "JkMount /jk-status status" >> /etc/httpd/conf/mod_jk.conf
service httpd restart
SCRIPT

	config.vm.define "Apache" do |s1|
		s1.vm.hostname = "Apache"
		s1.vm.network "public_network", bridge: "External"
		config.vm.provision "shell", inline: $git
		config.vm.provision "shell", inline: $web
		config.vm.provision "shell", inline: $ip1
	end

$ip2 = <<SCRIPT
echo "DEVICE=eth0" > /etc/sysconfig/network-scripts/ifcfg-eth0
echo "BOOTPROTO=none" >> /etc/sysconfig/network-scripts/ifcfg-eth0
echo "ONBOOT=yes" >> /etc/sysconfig/network-scripts/ifcfg-eth0
echo "IPADDR=192.168.1.202" >> /etc/sysconfig/network-scripts/ifcfg-eth0
echo "NETMASK=255.255.255.0" >> /etc/sysconfig/network-scripts/ifcfg-eth0
echo "GATEWAY=192.168.1.1" >> /etc/sysconfig/network-scripts/ifcfg-eth0
echo "DNS1=8.8.8.8" >> /etc/sysconfig/network-scripts/ifcfg-eth0
SCRIPT

	config.vm.define "Tom1" do |s2|
		s2.vm.hostname = "Tomcat1"
		s2.vm.network "public_network", bridge: "External"
		config.vm.provision "yum", type: "shell", inline: "yum install java-1.8.0-openjdk tomcat tomcat-webapps tomcat-admin-webapps -y"
		config.vm.provision "shell", inline: "systemctl start tomcat"
		config.vm.provision "shell", inline: "systemctl enable tomcat"
		config.vm.provision "shell", inline: "firewall-cmd --permanent --zone=public --add-port=8009/tcp"
		config.vm.provision "shell", inline: "firewall-cmd --reload"
		config.vm.provision "shell", inline: "mkdir /usr/share/tomcat/webapps/task2/"
		config.vm.provision "shell", inline: "echo 'Greetings from Tomcat1!!!' > /usr/share/tomcat/webapps/task2/index.html"
		config.vm.provision "shell", inline: $ip2
	end

$ip3 = <<SCRIPT
echo "DEVICE=eth0" > /etc/sysconfig/network-scripts/ifcfg-eth0
echo "BOOTPROTO=none" >> /etc/sysconfig/network-scripts/ifcfg-eth0
echo "ONBOOT=yes" >> /etc/sysconfig/network-scripts/ifcfg-eth0
echo "IPADDR=192.168.1.203" >> /etc/sysconfig/network-scripts/ifcfg-eth0
echo "NETMASK=255.255.255.0" >> /etc/sysconfig/network-scripts/ifcfg-eth0
echo "GATEWAY=192.168.1.1" >> /etc/sysconfig/network-scripts/ifcfg-eth0
echo "DNS1=8.8.8.8" >> /etc/sysconfig/network-scripts/ifcfg-eth0
SCRIPT

	config.vm.define "Tom2" do |s3|
		s3.vm.hostname = "Tomcat1"
		s3.vm.network "public_network", bridge: "External"
		config.vm.provision "yum", type: "shell", inline: "yum install java-1.8.0-openjdk tomcat tomcat-webapps tomcat-admin-webapps -y"
		config.vm.provision "shell", inline: "systemctl start tomcat"
		config.vm.provision "shell", inline: "systemctl enable tomcat"
		config.vm.provision "shell", inline: "firewall-cmd --permanent --zone=public --add-port=8009/tcp"
		config.vm.provision "shell", inline: "firewall-cmd --reload"
		config.vm.provision "shell", inline: "mkdir /usr/share/tomcat/webapps/task2/"
		config.vm.provision "shell", inline: "echo 'Greetings from Tomcat2!!!' > /usr/share/tomcat/webapps/task2/index.html"
		config.vm.provision "shell", inline: $ip3
	end
end
