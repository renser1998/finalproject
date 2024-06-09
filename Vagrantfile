
MACHINES = {
  app1: {
    networks: [
      { ip: "192.168.1.31", adapter: 2, netmask: "255.255.255.0", virtualbox__intnet: "dmz-net" },
      { ip: "192.168.56.31", adapter: 8 },
    ],
  },
  app2: {
    networks: [
      { ip: "192.168.1.32", adapter: 2, netmask: "255.255.255.0", virtualbox__intnet: "dmz-net" },
      { ip: "192.168.56.32", adapter: 8 },
    ],
  },
  fw: {
    networks: [
      { ip: "192.168.1.101",  adapter: 2, netmask: "255.255.255.0", virtualbox__intnet: "dmz-net" },
      { ip: "10.10.1.101",    adapter: 3, netmask: "255.255.255.0", virtualbox__intnet: "internal" },
      { ip: "192.168.56.101", adapter: 8 },
    ],
  },
  nfs: {
    networks: [
      { ip: "10.10.1.142",    adapter: 2, netmask: "255.255.255.0", virtualbox__intnet: "internal"},
      { ip: "192.168.56.142", adapter: 8 },
    ],
  },
  balancer: {
    networks: [
      { ip: "192.168.1.10",  adapter: 2, netmask: "255.255.255.0", virtualbox__intnet: "dmz-net" },
      { ip: "192.168.56.10", adapter: 8 },
    ]
  }
}

Vagrant.configure("2") do |config|
  MACHINES.each_with_index do |(boxname, boxconfig), index|
    config.vm.define boxname do |box|
      box.vm.provider "virtualbox" do |v|
        v.memory = 1024
        v.cpus = 1
      end
      box.vm.box = "generic/centos8s"
      #box.vm.box = "centos/7"
      #box.vm.box_version = "2004.01"
      box.vm.host_name = boxname.to_s

      boxconfig[:networks].each do |ipconf|
        box.vm.network "private_network", **ipconf

        box.vm.provision "shell", inline: <<-SHELL
        cd /etc/yum.repos.d/
        sudo sed -i 's/mirrorlist/#mirrorlist/g' /etc/yum.repos.d/CentOS-*
        sudo sed -i 's|#baseurl=http://mirror.centos.org|baseurl=http://vault.centos.org|g' /etc/yum.repos.d/CentOS-*
        #yum install -y wget
    SHELL
      end

      # --- НЕ запускаем автоматом, т.к. плейбук отрабатывает, если поднять
      #     одну ВМ obs
      # # --- ansible запускаем после запуска всех машин из MACHINES
      # #     для запуска индивидуальных плейбуков необходимо использовать
      # #     команду:
      # #     ansible-playbook -i ansible/hosts ansible/playbook-name.yaml
      # if index == (MACHINES.length - 1)
      #   box.vm.provision "ansible" do |ansible|
      #     ansible.playbook = "ansible/provision.yaml"
      #     ansible.inventory_path = "ansible/hosts"
      #     ansible.host_key_checking = "false"
      #     ansible.vault_password_file = "ansible/vault_pass"
      #     ansible.limit = "all"
      #   end
      # end
    end
  end
end
