Vagrant.configure(2) do |config|
	config.vm.box = "cdaf/CentOSLVM"
	config.vm.synced_folder ".", "/vagrant", disabled: true
	config.vm.provider "hyperv" do |hv|
		hv.ip_address_timeout = 300
	end
	config.vm.provision "yum", type: "shell",
		inline: "yum install mc -y"

$git = <<SCRIPT
yum install git -y
git config --global user.name "One Cent"
git config --global user.email "One.Cent@test.com"
git init
git clone https://github.com/LLlnak/training_devops
cd training_devops
git checkout task1
SCRIPT

	config.vm.define "s1" do |s1|
		s1.vm.hostname = "OneCent"
		config.vm.provision "shell", inline: $git
	end

	config.vm.define "s2" do |s2|
		s2.vm.hostname = "TwoCents"
	end

end
