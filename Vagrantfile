proxy_timeout = 500000
server = ENV['SERVER_HOST']
user = ENV['LIBVIRT_USER']
nodes = [
  { :hostname => 'dev-db-eu-1', :ip => '172.16.0.43' },
  { :hostname => 'dev-db-eu-2', :ip => '172.16.0.44' }
]
Vagrant.configure("2") do |config|
  nodes.each do |node|
    config.vm.define node[:hostname] do |nodeconfig|
      nodeconfig.vm.box = "debian/stretch64"
      nodeconfig.vm.hostname = node[:hostname]
      nodeconfig.ssh.insert_key = false
      nodeconfig.vm.synced_folder ".", "/vagrant", disabled: true
      nodeconfig.ssh.forward_agent = true
      nodeconfig.ssh.proxy_command = "ssh #{user}@#{server} netcat -w #{proxy_timeout} %h %p"
      nodeconfig.vm.network "public_network", bridge: "ens6", dev: "br0", mode: "bridge", type: "bridge"
      nodeconfig.vm.provider :libvirt do |libvirt|
        libvirt.uri = "qemu+ssh://infra@vms4/system"
        libvirt.storage_pool_name = "default"
        libvirt.connect_via_ssh
        libvirt.memory = 4096
        libvirt.cpus = 4
        libvirt.cputopology :sockets => '2', :cores => '2', :threads => '1'
        libvirt.machine_virtual_size = 100
        nodeconfig.vm.provision "ansible", type: "ansible", playbook: "site.yml"
        
    end  
    end    
  end
end