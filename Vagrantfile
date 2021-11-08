# -*- mode: ruby -*-
# vi: set ft=ruby :

VAGRANTFILE_API_VERSION = "2"

$domain = "example.com"
$subnet = "192.168.56"
$gui_mode =  true # virtualbox will fail on macos monterey in headless mode
$ssh_port_prefix = "22"

$es_num_instances = 3

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.box = "ubuntu/focal64"
  config.ssh.insert_key = false

  # elasticsearch instances
  (1..$es_num_instances).each do |i|
    config.vm.define vm_name = "%s%01d" % ["elasticsearch", i] do |node|
      node.vm.hostname = vm_name

      node.vm.provider :virtualbox do |vb|
        vb.gui = $gui_mode
        vb.cpus = 2
        vb.memory = 2048
      end

      node.vm.network :private_network, ip: "#{$subnet}.#{i+50}"
      node.vm.network "forwarded_port", guest: 22, host: "#{$ssh_port_prefix}5#{i}", id: "ssh"
    end
  end

  # logstash instance
  config.vm.define "logstash" do |node|
    node.vm.hostname = "logstash.#{$domain}"
    
    node.vm.provider :virtualbox do |vb|
      vb.gui = $gui_mode
      vb.memory = 2048
    end
    
    node.vm.network :private_network, ip: "#{$subnet}.61"
    node.vm.network "forwarded_port", guest: 22, host: "#{$ssh_port_prefix}61", id: "ssh"
  end
  
  # kibana instance
  config.vm.define "kibana" do |node|
    node.vm.hostname = "kibana.#{$domain}"
    
    node.vm.provider :virtualbox do |vb|
      vb.gui = $gui_mode
      vb.memory = 1024
    end
    
    node.vm.network :private_network, ip: "#{$subnet}.62"
    node.vm.network "forwarded_port", guest: 22, host: "#{$ssh_port_prefix}62", id: "ssh"
    node.vm.network "forwarded_port", guest: 5601, host: "5601", id: "kibana"
  end

  # prometheus instance
  config.vm.define "prometheus" do |node|
    node.vm.hostname = "prometheus.#{$domain}"
    
    node.vm.provider :virtualbox do |vb|
      vb.gui = $gui_mode
      vb.memory = 1024
    end
    
    node.vm.network :private_network, ip: "#{$subnet}.63"
    node.vm.network "forwarded_port", guest: 22, host: "#{$ssh_port_prefix}63", id: "ssh"
    
    # provision with ansible
    node.vm.provision "ansible" do |ansible|
      ansible.limit = "all"
      ansible.playbook = "site_local.yml"
      ansible.become = true
      ansible.galaxy_role_file = "requirements.yml"
      ansible.vault_password_file = "vault_pass_local.txt"
      ansible.groups = {
        "all:vars" => { "ansible_python_interpreter" => "/usr/bin/python3" },
        "elasticsearch" => [ "elasticsearch1", "elasticsearch2", "elasticsearch3" ],
        "logstash" => [ "logstash" ],
        "kibana" => [ "kibana" ],
        "prometheus" => [ "prometheus" ]
      }
      # ansible verbosity and tags
      #ansible.verbose  = "vvvv"
      ansible.tags = [ "base", "logstash" ]
    end
  end
end
