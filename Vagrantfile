BOX = 'generic/rhel8'
MANAGED_COUNT = '2'
MANAGED_NAME = 'mnode'
CONTROL_NAME = 'cnode'
DOMAIN = 'example.az'
SUBNET = '10.10.10'
EXTRA_DISK_SIZE = 1024
CPU = '1' 
MEMORY = '1024'
$PROVISION = <<-SCRIPT
for i in $(seq 1 #{MANAGED_COUNT}); do
    sudo echo "#{SUBNET}.$(($i+10)) #{MANAGED_NAME}$i #{MANAGED_NAME}$i.#{DOMAIN}" >> /etc/hosts
done
sudo echo "#{SUBNET}.10 #{CONTROL_NAME} #{CONTROL_NAME}.#{DOMAIN}" >> /etc/hosts
mkdir -p /root/.ssh
if [ $(hostname) != #{CONTROL_NAME} ]; then
    dnf update -y
    mv /tmp/id_rsa.pub /root/.ssh/authorized_keys
elif [ $(hostname) == #{CONTROL_NAME} ]; then
    dnf update -y
    pip3 install --upgrade pip
    pip3 install ansible 
    mv /tmp/id_rsa* /root/.ssh/
fi
chown root:root -R /root/.ssh
chmod -R 600 /root/.ssh
SCRIPT

Vagrant.configure("2") do |config|

    config.vm.box_check_update = false
  
    (1..MANAGED_COUNT.to_i).each do |i|
      config.vm.define "#{MANAGED_NAME}#{i}" do |node|
        disk_file = "./storage/disk#{i}.vdi"
        node.vm.box = "#{BOX}"
        node.vm.hostname = "#{MANAGED_NAME}#{i}"
        node.vm.network "private_network", ip: "#{SUBNET}.#{i + 10}"
        node.vm.provider "virtualbox" do |vb|\
            vb.name = "EX294_#{MANAGED_NAME}#{i}"
            vb.check_guest_additions = false
            vb.cpus = "#{CPU}"
            vb.memory = "#{MEMORY}"
            if "#{i}" == "#{MANAGED_COUNT}"
                unless File.exist? disk_file
                vb.customize ['createhd', '--filename', disk_file, '--size', EXTRA_DISK_SIZE]
                end
                vb.customize ['storageattach', :id, '--storagectl', 'IDE Controller', '--port', 1, '--device', 0, '--type', 'hdd', '--medium', disk_file]
            end
        end
        node.vm.provision "file", source: "./id_rsa.pub", destination: "/tmp/id_rsa.pub"
        node.vm.provision "shell", inline: $PROVISION
      end
    end

    config.vm.define "#{CONTROL_NAME}" do |node|
        node.vm.box = "#{BOX}"
        node.vm.hostname = "#{CONTROL_NAME}"
        node.vm.network "private_network", ip: "#{SUBNET}.10"
        node.vm.provider "virtualbox" do |vb|
            vb.name = "EX294_#{CONTROL_NAME}"
            vb.check_guest_additions = false
            vb.cpus = "#{CPU}"
            vb.memory = "#{MEMORY}"
        node.vm.provision "file", source: "./id_rsa", destination: "/tmp/id_rsa"
        node.vm.provision "file", source: "./id_rsa.pub", destination: "/tmp/id_rsa.pub"  
        node.vm.provision "shell", inline: $PROVISION
        end
    end
end