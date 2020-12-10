IMAGE_NAME = "centos/7"
N = 1
ANSIBLE_SERVERIP = "192.168.219.200"
MASTER_HOST = "kubernetes-master"
K8SMASTER_IP = "192.168.219.100"
NFS_IP = "192.168.219.201"
K8SWORKER_IP = "192.168.219."

Vagrant.configure("2") do |config|
  config.ssh.insert_key = false

  # master node
  config.vm.define MASTER_HOST do |cfg|
    cfg.vm.box = IMAGE_NAME
    cfg.vm.network "public_network", ip: K8SMASTER_IP
    cfg.vm.hostname = MASTER_HOST
    
    cfg.vm.provider "virtualbox" do |v|
      v.memory = 4096
      v.cpus = 4
      v.name = MASTER_HOST
    end
    cfg.vm.provision "shell", inline: <<-SCRIPT
      sed -i -e 's/PasswordAuthentication no/PasswordAuthentication yes/g' /etc/ssh/sshd_config
      systemctl restart sshd
    SCRIPT
  end

  # worker node
  (1..N).each do |i|
    config.vm.define "client-#{i}" do |cfg|
      cfg.vm.box = IMAGE_NAME
      cfg.vm.network "public_network", ip: K8SWORKER_IP + "#{i+10}"
      cfg.vm.hostname = "client-#{i}"
      
      cfg.vm.provider "virtualbox" do |v|
        v.memory = 8192
        v.cpus = 4
        v.name = "client#{i}"
      end
      cfg.vm.provision "shell", inline: <<-SCRIPT
        sed -i -e 's/PasswordAuthentication no/PasswordAuthentication yes/g' /etc/ssh/sshd_config
        systemctl restart sshd
      SCRIPT
    end
  end

  # nfs server
  config.vm.define "nfs-server" do |cfg|
    cfg.vm.box = IMAGE_NAME
    cfg.vm.network "public_network", ip: NFS_IP
    cfg.vm.hostname = "nfs-server"
    
    cfg.vm.provider "virtualbox" do |v|
      v.memory = 2048
      v.cpus = 2
      v.name = "nfs-server"
    end
    cfg.vm.provision "shell", inline: <<-SCRIPT
      sed -i -e 's/PasswordAuthentication no/PasswordAuthentication yes/g' /etc/ssh/sshd_config
      systemctl restart sshd
    SCRIPT
  end

  # ansible-server
  config.vm.define "ansible-server4" do |cfg|
    cfg.vm.box = IMAGE_NAME
    cfg.vm.hostname = "ansible-server-4"
    cfg.vm.network "public_network", ip: ANSIBLE_SERVERIP

    cfg.vm.provider "virtualbox" do |v|
      v.memory = 2048
      v.cpus = 2
      v.name =  "ansible-server-4"
    end

    cfg.vm.provision  "shell", inline: <<-SCRIPT
      yum install epel-release -y
      yum install python36 libselinux-python3 -y 
      yum install sshpass git -y
      sudo pip3 install ansible
      sed -i -e 's/PasswordAuthentication no/PasswordAuthentication yes/g' /etc/ssh/sshd_config
      systemctl restart sshd
    SCRIPT

    # copy ansible files
    cfg.vm.provision "file", source: "./ansible_workspace", destination: "ansible_workspace"
    cfg.vm.provision "shell", inline: "ansible-playbook ./ansible_workspace/add_hosts.yaml", privileged: false
    cfg.vm.provision "shell", inline: "ansible-playbook ./ansible_workspace/configure_ssh.yaml -i /home/vagrant/hosts", privileged: false
    
  end
end