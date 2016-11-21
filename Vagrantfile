Vagrant.configure(2) do |config|
  config.vm.box = "ubuntu/trusty64"
  config.vm.network "private_network", ip: "192.168.99.100"
  
  config.vm.provider "virtualbox" do |vb|
    vb.memory = "4096"
    vb.name = "mongodb-cluster"
  end
  
  config.vm.hostname = "mongodb-cluster.local"
end
