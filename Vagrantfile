BOX = 'generic/rhel8'
NODES_COUNT = '3'
MANAGED_NAME_PREFIX = 'mnode'
CONTROL_NAME = 'cnode'
DOMAIN = 'example.az'
SUBNET = '10.10.10'
EXTRA_DISK_SIZE = 1024
$PROVISION_HOSTS = <<-SCRIPT
for i in $(seq 1 #{NODES_COUNT}); do
    sudo echo "#{SUBNET}.$(($i+10)) #{MANAGED_NAME_PREFIX}$i #{MANAGED_NAME_PREFIX}$i.#{DOMAIN}" >> /etc/hosts
done
sudo echo "#{SUBNET}.10 #{CONTROL_NAME} #{CONTROL_NAME}.#{DOMAIN}" >> /etc/hosts
SCRIPT

Vagrant.configure("2") do |config|

    config.vm.box_check_update = false
  
    (1..NODES_COUNT.to_i).each do |i|
      config.vm.define "#{MANAGED_NAME_PREFIX}#{i}" do |node|
        disk_file = "./storage/disk#{i}.vdi"
        node.vm.box = "#{BOX}"
        node.vm.hostname = "#{MANAGED_NAME_PREFIX}#{i}"
        node.vm.network "private_network", ip: "#{SUBNET}.#{i + 10}"
        node.vm.provider "virtualbox" do |vb|\
            vb.name = "EX294_#{MANAGED_NAME_PREFIX}#{i}"
            vb.check_guest_additions = false
            vb.cpus = 1
            vb.memory = 1024
            if "#{i}" == "#{NODES_COUNT}"
                unless File.exist? disk_file
                vb.customize ['createhd', '--filename', disk_file, '--size', EXTRA_DISK_SIZE]
                end
                vb.customize ['storageattach', :id, '--storagectl', 'IDE Controller', '--port', 1, '--device', 0, '--type', 'hdd', '--medium', disk_file]
            end
        end
        node.vm.provision "shell", inline: $PROVISION_HOSTS
        node.vm.provision "file", source: "./id_rsa.pub", destination: "/root/.ssh/authorized_keys"
      end
    end

    config.vm.define "#{CONTROL_NAME}" do |node|
        node.vm.box = "#{BOX}"
        node.vm.hostname = "#{CONTROL_NAME}"
        node.vm.provision "shell", inline: <<-INPUT
            sudo dnf update -y
            sudo pip3 install --upgrade pip
            sudo pip3 install ansible 
        INPUT
        node.vm.provision "shell", inline: $PROVISION_HOSTS
        node.vm.provision "file", source: "./id_rsa", destination: "/root/.ssh/id_rsa"
        node.vm.provision "file", source: "./id_rsa.pub", destination: "/root/.ssh/id_rsa.pub"
        node.vm.network "private_network", ip: "#{SUBNET}.10"
        node.vm.provider "virtualbox" do |vb|
            vb.name = "EX294_#{CONTROL_NAME}"
            vb.check_guest_additions = false
            vb.cpus = 1
            vb.memory = 1024
        end
    end
end