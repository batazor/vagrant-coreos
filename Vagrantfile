# -*- mode: ruby -*-
# # vi: set ft=ruby :

require 'fileutils'
require 'open-uri'
require 'tempfile'
require 'yaml'

# PATH
CLOUD_CONFIG_PATH    = File.expand_path("template/cloud-config/user_data.yaml")
ETCD_CONFIG_PATH     = File.expand_path("template/cloud-config/etcd-member.yaml")
FLANNEL_CONFIG_PATH  = File.expand_path("template/cloud-config/flannel.yaml")

def getIP(num)
  return "172.17.8.#{num+100}"
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

  # ETCD =========================================================================
  etcdIPs = [*1..$num_instances].map{ |i| getIP(i) }
  initial_etcd_cluster = etcdIPs.map.with_index{ |ip, i| "#{"%s-%02d" % [$instance_name_prefix, (i + 1)]}=http://#{ip}:2380" }.join(",")


  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://atlas.hashicorp.com/search.
  config.vm.box = "coreos-%s" % $update_channel
  if $image_version != "current"
      config.vm.box_version = $image_version
  end
  config.vm.box_url = "https://storage.googleapis.com/%s.release.core-os.net/amd64-usr/%s/coreos_production_vagrant.json" % [$update_channel, $image_version]

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

      # NETWORK ================================================================
      # Create a private network, which allows host-only access to the machine
      # using a specific IP.
      config.vm.network :private_network, ip: getIP(i)

      # COREOS CONFIG ==========================================================
      user_data = YAML.load(IO.readlines(CLOUD_CONFIG_PATH)[1..-1].join)

      # ETCD CONFIG ============================================================
      etcd = YAML.load(IO.readlines(ETCD_CONFIG_PATH)[1..-1].join)

      etcd_units = etcd['coreos']['units']
      etcd_units[0]['content'] = etcd_units[0]['content'] % {
        :ETCD_NODE_NAME => vm_name,
        :ETCD_INITIAL_CLUSTER => initial_etcd_cluster,
        :ETCD_IMAGE_TAG => ENV['ETCD_IMAGE_TAG']
      }

      user_data["coreos"]["units"] += etcd_units

      # FLANNEL CONFIG =========================================================
      flannel = YAML.load(IO.readlines(FLANNEL_CONFIG_PATH)[1..-1].join)
      user_data["coreos"]["units"] += flannel["coreos"]["units"]

      # ETCD CONFIG ============================================================

      etcd_config_file = Tempfile.new('etcd_config', :binmode => true)
      etcd_config_file.write("#cloud-config\n#{user_data.to_yaml}")
      etcd_config_file.close

      config.vm.provision :file, :source => etcd_config_file.path, :destination => "/tmp/vagrantfile-user-data"
      config.vm.provision :shell, :inline => "mv /tmp/vagrantfile-user-data /var/lib/coreos-vagrant/", :privileged => true

      # SYNCED FOLDERS =========================================================
      config.vm.synced_folder "cert", "/home/core/cert", type: "nfs"

      config.nfs.map_uid = Process.uid
      config.nfs.map_gid = Process.gid
    end
  end
end
