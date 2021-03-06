# -*- mode: ruby -*-
# # vi: set ft=ruby :

require 'fileutils'
require 'open-uri'
require 'tempfile'
require 'yaml'

# PATH
CLOUD_CONFIG_PATH    = File.expand_path("template/cloud-config/user_data.yaml")

def getIP(num)
  return "172.17.7.#{num+100}"
end

Vagrant.configure("2") do |config|
  # Enable plugins =============================================================
  config.env.enable # https://github.com/gosuri/vagrant-env

  # ENV ========================================================================
  # Defaults for config options defined in CONFIG
  $num_instances = ENV['NUM_INSTANCES'].to_i

  $instance_name_prefix = ENV['INSTANCE_NAME_PREFIX']
  $update_channel       = ENV['UPDATE_CHANNEL']
  $image_version        = ENV['IMAGE_VERSION']

  $vm_gui             = ENV['VM_GUI'].to_s == "true"
  $vm_memory          = ENV['VM_MEMORY'].to_i
  $vm_cpus            = ENV['VM_CPUS'].to_i
  $vb_cpuexecutioncap = ENV['VB_CPUEXECUTIONCAP'].to_i

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://atlas.hashicorp.com/search.
  config.vm.box = "coreos-%s" % $update_channel
  if $image_version != "current"
      config.vm.box_version = $image_version
  end
  config.vm.box_url = "https://storage.googleapis.com/%s.release.core-os.net/amd64-usr/%s/coreos_production_vagrant_vmware_fusion.json" % [$update_channel, $image_version]

  config.vm.provider :virtualbox do |v|
    # On VirtualBox, we don't have guest additions or a functional vboxsf
    # in CoreOS, so tell Vagrant that so it can be smarter.
    v.check_guest_additions = false
    v.functional_vboxsf     = false
  end

  (1..$num_instances).each do |i|
    config.vm.define vm_name = "%s-%02d" % [$instance_name_prefix, i] do |config|

      # Common settin machine ==================================================
      config.vm.hostname = vm_name

      config.vm.provider :virtualbox do |v|
        v.gui = $vm_gui
        v.memory = $vm_memory
        v.cpus = $vm_cpus
        v.customize ["modifyvm", :id, "--cpuexecutioncap", "#{$vb_cpuexecutioncap}"]
      end

      # SSH settings ===========================================================
      config.ssh.insert_key = false
      config.ssh.private_key_path = ["~/.ssh/id_rsa", "~/.vagrant.d/insecure_private_key"]
      config.vm.provision "file", source: "~/.ssh/id_rsa.pub", destination: "~/.ssh/authorized_keys"
      config.vm.provision "shell", inline: <<-EOC
        sudo sed -i -e "\\#PasswordAuthentication yes# s#PasswordAuthentication yes#PasswordAuthentication no#g" /etc/ssh/sshd_config
        sudo systemctl restart sshd.socket
      EOC

      # NETWORK ================================================================
      # Create a private network, which allows host-only access to the machine
      # using a specific IP.
      config.vm.network :private_network, ip: getIP(i)

      # COREOS CONFIG ==========================================================
      # user_data = YAML.load(IO.readlines(CLOUD_CONFIG_PATH)[1..-1].join)
      config.vm.provision :file, :source => "#{CLOUD_CONFIG_PATH}", :destination => "/tmp/vagrantfile-user-data"
      config.vm.provision :shell, inline: "mkdir -p /var/lib/coreos-vagrant", :privileged => true
      config.vm.provision :shell, :inline => "mv /tmp/vagrantfile-user-data /var/lib/coreos-vagrant/", :privileged => true
    end
  end
end
