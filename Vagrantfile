proxy_timeout = 500000
server = ENV['SERVER_HOST']
user = ENV['LIBVIRT_USER']
nodes = [
  { :hostname => 'dev-db-1', :ip => '172.16.10.51', :box => 'debian/stretch64', :ram => 2048 },
  { :hostname => 'dev-db-2', :ip => '172.16.10.52', :box => 'debian/stretch64', :ram => 2048 },
  { :hostname => 'dev-db-3', :ip => '172.16.10.53', :box => 'debian/stretch64', :ram => 2048 }
]
Vagrant.configure("2") do |config|
  nodes.each do |node|
    config.vm.define node[:hostname] do |nodeconfig|
      nodeconfig.vm.box = "debian/stretch64"
      nodeconfig.vm.hostname = node[:hostname]
      #nodeconfig.vm.customize ["modifyvm", :id, "--memory", 2048]
      nodeconfig.ssh.insert_key = false
      nodeconfig.vm.synced_folder ".", "/vagrant", disabled: true
      nodeconfig.ssh.forward_agent = true
      nodeconfig.ssh.proxy_command = "ssh #{user}@#{server} netcat -w #{proxy_timeout} %h %p"
      nodeconfig.vm.network "public_network", bridge: "ens6", dev: "br0", mode: "bridge", type: "bridge", ip: node[:ip]
      nodeconfig.vm.provider :libvirt do |libvirt|
        libvirt.uri = "qemu+ssh://infra@vms8/system"
        libvirt.storage_pool_name = "vm"
        libvirt.connect_via_ssh
        libvirt.memory = 2048
      nodeconfig.vm.provision "ansible", playbook: "site.yml"
    end  
    end    
  end
end