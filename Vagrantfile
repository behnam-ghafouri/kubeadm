Vagrant.configure("2") do |config|
  config.vm.provision "shell", inline: <<-SHELL
      echo "root:123" | chpasswd
      echo "123" | sudo -S su -
      apt-get update -y
      echo "10.0.0.10  master-node" >> /etc/hosts
      echo "10.0.0.11  worker-node01" >> /etc/hosts
      echo "10.0.0.12  worker-node02" >> /etc/hosts
  SHELL
  
  config.vm.define "master" do |master|
    master.vm.box = "bento/ubuntu-22.04"
    master.vm.hostname = "master-node"
    master.vm.network "private_network", ip: "10.0.0.10"
    master.vm.provider "virtualbox" do |vb|
        vb.memory = 4048
        vb.cpus = 2
    end
    master.vm.provision "shell", inline: <<-NODE_SHELL
      echo "123" | sudo -S su -
      if [ "$EUID" -ne 0 ]; then
        echo "This script must be run as root" 
        exit 1
      fi
      cd /
      git clone https://github.com/behnam-ghafouri/kubeadm.git
      cd ./kubeadm/scripts
      ./common.sh
      ./master.sh
    NODE_SHELL
  end
  
  (1..2).each do |i|
    config.vm.define "node0#{i}" do |node|
      node.vm.box = "bento/ubuntu-22.04"
      node.vm.hostname = "worker-node0#{i}"
      node.vm.network "private_network", ip: "10.0.0.1#{i}"
      node.vm.provider "virtualbox" do |vb|
          vb.memory = 2048
          vb.cpus = 1
      end
      node.vm.provision "shell", inline: <<-NODE_SHELL
        echo "123" | sudo -S su -
        if [ "$EUID" -ne 0 ]; then
          echo "This script must be run as root" 
          exit 1
        fi
        cd /
        git clone https://github.com/behnam-ghafouri/kubeadm.git
        cd ./kubeadm/scripts
        ./common.sh
      NODE_SHELL
    end
  end
end
