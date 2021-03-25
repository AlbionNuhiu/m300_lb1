# CheckMk Vagrant 
#
#
# CheckMk Vagrant 
#
#
VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.define "checkmk" do |checkmk|
    checkmk.vm.box = "ubuntu/xenial64"
    checkmk.vm.hostname = "server"
    checkmk.vm.network "private_network", ip:"192.168.55.100" 
		checkmk.vm.network "forwarded_port", guest:80, host:8080, auto_correct: true
		checkmk.vm.provider "virtualbox" do |vb|
	  vb.memory = "4096"  
	end  
	
