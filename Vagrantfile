# -*- mode: ruby -*-
# vi: set ft=ruby :

## configuration variables ##
# max number of worker nodes
N = 2 
# each of components to install 
k8s_V = '1.30.4 '           # Kubernetes 
docker_V = '26.1.2'         # Docker  
ctrd_V = '1.7.21'           # Containerd 
## /configuration variables ##

Vagrant.configure("2") do |config|
  
  #=============#
  # Master Node #
  #=============#

    config.vm.define "m-k8s-#{k8s_V[0..5]}" do |cfg|
      cfg.vm.box = "sysnet4admin/CentOS-k8s"
      cfg.vm.provider "virtualbox" do |vb|
        vb.name = "m-k8s-#{k8s_V[0..5]}"
        vb.cpus = 3
        vb.memory = 3072
        vb.customize ["modifyvm", :id, "--groups", "/k8s-C#{k8s_V[0..5]}"]
      end
      cfg.vm.host_name = "m-k8s"
      cfg.vm.network "private_network", ip: "192.168.1.10"
      cfg.vm.network "forwarded_port", guest: 22, host: 60010, auto_correct: true, id: "ssh"
      cfg.vm.synced_folder "../data", "/vagrant", disabled: true
#      cfg.vm.synced_folder "../", "/vagrant"
      cfg.vm.provision "shell", path: "k8s_env_build.sh", args: N
      cfg.vm.provision "shell", path: "k8s_pkg_cfg.sh", args: [ k8s_V, docker_V, ctrd_V ]
      cfg.vm.provision "shell", path: "master_node.sh"
    end

  #==============#
  # Worker Nodes #
  #==============#

  (1..N).each do |i|
    config.vm.define "w#{i}-k8s-#{k8s_V[0..5]}" do |cfg|
      cfg.vm.box = "sysnet4admin/CentOS-k8s"
      cfg.vm.provider "virtualbox" do |vb|
        vb.name = "w#{i}-k8s-#{k8s_V[0..5]}"
        vb.cpus = 2
        vb.memory = 2048
        vb.customize ["modifyvm", :id, "--groups", "/k8s-C#{k8s_V[0..5]}"]
      end
      cfg.vm.host_name = "w#{i}-k8s"
      cfg.vm.network "private_network", ip: "192.168.1.10#{i}"
      cfg.vm.network "forwarded_port", guest: 22, host: "6010#{i}", auto_correct: true, id: "ssh"
      cfg.vm.synced_folder "../data", "/vagrant", disabled: true
      cfg.vm.provision "shell", path: "k8s_env_build.sh", args: N
      cfg.vm.provision "shell", path: "k8s_pkg_cfg.sh", args: [ k8s_V, docker_V, ctrd_V ]
      cfg.vm.provision "shell", path: "work_nodes.sh"
    end
  end

end
