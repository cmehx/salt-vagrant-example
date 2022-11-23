IP_NW="10.0.0."
S_ONE=10
S_TWO=11

Vagrant.configure("2") do |config|
  config.vm.define "salt_client_one" do |salt_client_one|
    salt_client_one.vm.box = "generic/ubuntu2204"
    salt_client_one.vm.hostname = 'minion'
    salt_client_one.vm.network "public_network"
    salt_client_one.vm.provider "virtualbox" do |vb|
      vb.memory = 1024
      vb.cpus = 1
    end
  end
  config.vm.define "salt_master" do |salt_master|
    salt_master.vm.box = "generic/ubuntu2204"
    salt_master.vm.hostname = 'master'
    salt_master.vm.network "public_network"
    salt_master.vm.provider "virtualbox" do |vb|
      vb.memory = 1024
      vb.cpus = 1
    end
  end
end
