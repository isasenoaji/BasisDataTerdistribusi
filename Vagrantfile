# -*- mode: ruby -*-
# vi: set ft=ruby :


Vagrant.configure("2") do |config|
  config.vm.box = "bento/ubuntu-16.0.4"

  config.vm.define "manager" do |manager|
    # Box Settings
    manager.vm.box = "bento/ubuntu-16.04"
    manager.vm.hostname = "manager"

    # Provider Settings
    manager.vm.provider "virtualbox" do |vb|
      # Display the VirtualBox GUI when booting the machine
      vb.gui = false
      vb.name = "manager"
      # Customize the amount of memory on the VM:
      vb.memory = "712"
    end
    manager.vm.network "private_network", ip: "192.168.31.100"
    manager.vm.provision "shell", path: "provision/bootstrap.sh", privileged: false
    #manager.vm.provision "shell", path: "provision/bootstrap-manager.sh", privileged: false
  end

  config.vm.define "data1" do |data1|
    # Box Settings
    data1.vm.box = "bento/ubuntu-16.04"
    data1.vm.hostname = "data1"

    # Provider Settings
    data1.vm.provider "virtualbox" do |vb|
      # Display the VirtualBox GUI when booting the machine
      vb.gui = false
      vb.name = "data1"
      # Customize the amount of memory on the VM:
      vb.memory = "712"
    end
    data1.vm.network "private_network", ip: "192.168.31.101"
    data1.vm.provision "shell", path: "provision/bootstrap.sh", privileged: false
    #data1.vm.provision "shell", path: "provision/bootstrap-datanode.sh", privileged: false
  end

  config.vm.define "data3" do |data3|
    # Box Settings
    data3.vm.box = "bento/ubuntu-16.04"
    data3.vm.hostname = "data3"

    # Provider Settings
    data3.vm.provider "virtualbox" do |vb|
      # Display the VirtualBox GUI when booting the machine
      vb.gui = false
      vb.name = "data3"
      # Customize the amount of memory on the VM:
      vb.memory = "712"
    end
    data3.vm.network "private_network", ip: "192.168.31.102"
    data3.vm.provision "shell", path: "provision/bootstrap.sh", privileged: false
    #data3.vm.provision "shell", path: "provision/bootstrap-datanode.sh", privileged: false
  end

  config.vm.define "data2" do |data2|
    # Box Settings
    data2.vm.box = "bento/ubuntu-16.04"
    data2.vm.hostname = "data2"

    # Provider Settings
    data2.vm.provider "virtualbox" do |vb|
      # Display the VirtualBox GUI when booting the machine
      vb.gui = false
      vb.name = "data2"
      # Customize the amount of memory on the VM:
      vb.memory = "712"
    end
    data2.vm.network "private_network", ip: "192.168.31.103"
    data2.vm.provision "shell", path: "provision/bootstrap.sh", privileged: false
    #data2.vm.provision "shell", path: "provision/bootstrap-datanode.sh", privileged: false
  end
  
  config.vm.define "service1" do |service1|
    # Box Settings
    service1.vm.box = "bento/ubuntu-16.04"
    service1.vm.hostname = "service1"

    # Provider Settings
    service1.vm.provider "virtualbox" do |vb|
      vb.gui = false
      vb.name = "service1"
      vb.memory = "1024"
    end
    service1.vm.network "private_network", ip: "192.168.31.104"
    service1.vm.provision "shell", path: "provision/bootstrap.sh", privileged: false
    #service1.vm.provision "shell", path: "provision/bootstrap-service.sh", privileged: false
  end

  config.vm.define "service2" do |service2|
    # Box Settings
    service2.vm.box = "bento/ubuntu-16.04"
    service2.vm.hostname = "service2"

    # Provider Settings
    service2.vm.provider "virtualbox" do |vb|
      vb.gui = false
      vb.name = "service2"
      vb.memory = "712"
    end
    service2.vm.network "private_network", ip: "192.168.31.105"
    service2.vm.provision "shell", path: "provision/bootstrap.sh", privileged: false
    #service2.vm.provision "shell", path: "provision/bootstrap-service.sh", privileged: false
  end

  config.vm.define "proxy" do |proxy|
    # Box Settings
    proxy.vm.box = "bento/ubuntu-16.04"
    proxy.vm.hostname = "proxy"

    # Provider Settings
    proxy.vm.provider "virtualbox" do |vb|
      vb.gui = false
      vb.name = "proxy"
      vb.memory = "712"
    end
    proxy.vm.network "private_network", ip: "192.168.31.106"
    proxy.vm.provision "shell", path: "provision/bootstrap.sh", privileged: false
    #proxy.vm.provision "shell", path: "provision/bootstrap-proxy.sh", privileged: false
  end

end