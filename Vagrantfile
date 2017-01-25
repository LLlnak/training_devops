Vagrant.configure(2) do |config|
	config.vm.box = "bertvv/centos72"
	config.vm.provider "virtualbox" do |vb|
		vb.gui = true
		vb.memory = 2048
		vb.cpus = 2
	end
	config.vm.provision "yum", type: "shell", inline: "yum install mc -y"

#######Settings#######
$ip1 = "192.168.10.2"
$ip2 = "192.168.10.3"
$ip3 = "192.168.10.4"
$pWEB = 80
$pTOM = 8009
$pTOM1 = 8080
$pH1 = 20080
$pH2 = 20081
$pH3 = 20082

#######Scripts########

$vars =  <<SCRIPT
ip1="192.168.10.2"
ip2="192.168.10.3"
ip3="192.168.10.4"
pWEB=80
pTOM=8009
pTOM1=8080
pH1=20080
pH2=20081
pH3=20082
SCRIPT

$git = <<SCRIPT
yum install git -y
git init
git clone https://github.com/LLlnak/training_devops
cd training_devops
git checkout task2
SCRIPT

$tom = <<SCRIPT
yum install java-1.8.0-openjdk tomcat tomcat-webapps tomcat-admin-webapps -y
systemctl enable tomcat
systemctl start tomcat
firewall-cmd --permanent --zone=public --add-port=$pTOM/tcp
firewall-cmd --reload
mkdir /usr/share/tomcat/webapps/task2/
echo "Greetings from $HOSTNAME!!!" > /usr/share/tomcat/webapps/task2/index.html
SCRIPT

#######Deploy#######

	config.vm.provision "shell", inline: $vars

	config.vm.define "Apache" do |s1|
		s1.vm.hostname = "server1"
		s1.vm.network "private_network", ip: $ip1
		s1.vm.network "forwarded_port", guest: $pWEB, host: $pH1
		s1.vm.provision "shell", inline: $git
		s1.vm.provision "shell", inline: 
$vars
<<-SHELL
yum install httpd -y
systemctl enable httpd
systemctl start httpd
firewall-cmd --permanent --zone=public --add-port=$pWEB/tcp
firewall-cmd --permanent --zone=public --add-port=$pTOM/tcp
firewall-cmd --permanent --zone=public --add-port=$pTOM1/tcp
firewall-cmd --reload
cp /home/vagrant/training_devops/mod_jk.so /etc/httpd/modules/

echo "worker.list=lb
worker.lb.type=lb
worker.lb.balance_workers=tom1, tom2
worker.tom1.host=$ip2
worker.tom1.port=$pTOM
worker.tom1.type=ajp13
worker.tom2.host=$ip3
worker.tom2.port=$pTOM
worker.tom2.type=ajp13
worker.list=status
worker.status.type=status" > /etc/httpd/conf/workers.properties

echo "LoadModule jk_module modules/mod_jk.so
JkWorkersFile conf/workers.properties
JkShmFile /tmp/shm
JkLogFile logs/mod_jk.log
JkLogLevel info
JkMount /task2* lb
JkMount /jk-status status" > /etc/httpd/conf.d/mod_jk.conf

service httpd restart
SHELL

	end

	config.vm.define "Tomcat1" do |s2|
		s2.vm.hostname = "Tomcat1"
		s2.vm.network "private_network", ip: $ip2
		s2.vm.network "forwarded_port", guest: $pTOM1, host: $pH2
		s2.vm.provision "shell", inline:
$vars
$tom
	end

	config.vm.define "Tomcat2" do |s3|
		s3.vm.hostname = "Tomcat2"
		s3.vm.network "private_network", ip: $ip3
		s3.vm.network "forwarded_port", guest: $pTOM1, host: $pH3
		s3.vm.provision "shell", inline:
$vars
$tom
	end
end
