MACHINES = {
  :node1 => {
        :box_name => "generic/centos8s",
        :vm_name => "node1",
        :cpus => 2,
        :memory => 1024,
        :ip => "192.168.57.11",
  },
  :node2 => {
        :box_name => "generic/centos8s",
        :vm_name => "node2",
        :cpus => 2,
        :memory => 1024,
        :ip => "192.168.57.12",

  },
  :barman => {
        :box_name => "generic/centos8s",
        :vm_name => "barman",
        :cpus => 1,
        :memory => 1024,
        :ip => "192.168.57.13",

  },

}

Vagrant.configure("2") do |config|
  MACHINES.each do |boxname, boxconfig|
    config.vm.define boxname do |box|
      box.vm.box = boxconfig[:box_name]
      box.vm.host_name = boxconfig[:vm_name]
      box.vm.network "private_network", ip: boxconfig[:ip]
      box.vm.provider "virtualbox" do |v|
        v.memory = boxconfig[:memory]
        v.cpus = boxconfig[:cpus]
      end

      config.vm.provision "shell", inline: <<-SHELL
         mkdir -p ~root/.ssh
         cp ~vagrant/.ssh/auth* ~root/.ssh
         sed -i.bak 's/mirrorlist/#mirrorlist/g' /etc/yum.repos.d/CentOS-*
         sed -i.bak 's|#baseurl=http://mirror.centos.org|baseurl=http://vault.centos.org|g' /etc/yum.repos.d/CentOS-*
         sed -i 's/^#PasswordAuthentication.*$/PasswordAuthentication yes/' /etc/ssh/sshd_config
         systemctl restart sshd.service
      SHELL

      if boxconfig[:vm_name] == "barman"
       box.vm.provision "ansible" do |ansible|
        ansible.playbook = "ansible/provision.yml"
        ansible.inventory_path = "ansible/hosts"
        ansible.host_key_checking = "false"
        ansible.limit = "all"
       end
      end
    end
  end
end
