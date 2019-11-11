# -*- mode: ruby -*-
# vi: set ft=ruby :

require "yaml"
$config_file = 'vars.yml'
if !File.file?($config_file)
  raise "Vars file " + $config_file + " not found!"
end
$cfg = YAML.load_file($config_file)

Vagrant.configure("2") do |config|
  config.vm.box = $cfg['image']
  config.vm.network "private_network", ip: $cfg['private_ip']

  # Change the new default behavior to use our profile key for connecting - See https://stackoverflow.com/a/28524909/9428314
  config.ssh.insert_key = false

  config.vm.provider "virtualbox" do |v|
    if $cfg.include? 'vm_name' then
      v.name = $cfg['vm_name']
    end
    
    v.memory = $cfg['vm_memory']
    v.cpus = $cfg['vm_cpu_cores']
  end

  if $cfg['proxy_enabled'] then
    config.vm.provision "shell", args: [$cfg['proxy_cert_host']], inline: <<-SHELL
      echo -n | openssl s_client -connect $1 -showcerts | sed -ne '/-BEGIN CERTIFICATE-/,/-END CERTIFICATE-/p' > /etc/pki/ca-trust/source/anchors/custom-ca.crt
      update-ca-trust
    SHELL
  end

  puts "Installing tools"
  config.vm.provision "shell", inline: <<-SHELL
    yum install -y git vim

    yum install -y epel-release
    yum install -y htop
  SHELL
  config.vm.provision "shell", privileged: false, inline: <<-SHELL
    git clone --depth 1 https://github.com/junegunn/fzf.git ~/.fzf
    ~/.fzf/install --all
  SHELL

  $custom_script_path = "provision/custom-provisioner.sh"
  if File.file?($custom_script_path) then
    config.vm.provision "shell", privileged: false, path: $custom_script_path
  end
end

puts "Configuration proceeded for machine " + $cfg['private_ip']