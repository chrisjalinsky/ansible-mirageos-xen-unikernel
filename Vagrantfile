VAGRANTFILE_API_VERSION = "2"

base_dir = File.expand_path(File.dirname(__FILE__))

# Cluster, VM and network settings
NETWORK_SUBNET = "172.16.8"
NETWORK_DOMAIN_NAME = "test.lan"
NUM_XENS = 1
XEN_IPS_START = 143
XEN_MEMORY = 4096
XEN_CPU = 2
CLUSTER = {}

# Build the cluster
(1..NUM_XENS).each do |i|
  CLUSTER["xen#{i}.#{NETWORK_DOMAIN_NAME}"] = {:ip => "#{NETWORK_SUBNET}.#{XEN_IPS_START + i}",  :cpus => "#{XEN_CPU}", :mem => "#{XEN_MEMORY}", :short => "graphite#{i}"}
end


Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|

  if Vagrant.has_plugin?("vagrant-cachier")
    config.cache.scope = :machine
    config.cache.enable :apt
  end

  CLUSTER.each do |hostname, info|

    config.vm.define hostname do |cfg|

      cfg.vm.provider :virtualbox do |vb, override|
        override.vm.box = "ubuntu/trusty64"
        override.vm.network :private_network, ip: "#{info[:ip]}", adapter: 2
        override.vm.hostname = hostname
        vb.name = 'master-' + hostname
        vb.customize ["modifyvm", :id, "--memory", info[:mem], "--cpus", info[:cpus], "--hwvirtex", "on" ]
        vb.customize ["modifyvm", :id, "--nicpromisc2", "allow-all"]
      end

      # provision nodes with ansible
      cfg.vm.provision :ansible do |ansible|
        ansible.verbose = "v"
        ansible.playbook = base_dir + "/site.yml"
        ansible.inventory_path = base_dir + "/inventory/vagrant"
        ansible.limit = "#{hostname}"
        ansible.extra_vars = {
          vagrant_ip: "#{info[:ip]}"
        }
      end

    end

  end

end