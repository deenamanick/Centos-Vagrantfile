require 'socket'
hostname = Socket.gethostname
localmachineip = IPSocket.getaddress(Socket.gethostname)
puts %Q{ This machine has the IP '#{localmachineip} and host name '#{hostname}'}

# Vagrantfile API/syntax version.
VAGRANTFILE_API_VERSION = '2'
#variables par défaut
centos_box_name = 'centos/7'
NETWORK_BASE = '192.168.56'
INTEGRATION_START_SEGMENT = 24

$miscellany = <<SCRIPT
yum -y install wget git net-tools bind-utils iptables-services bridge-utils bash-completion kexec-tools sos psacct nfs-utils
yum clean all ; yum repolist
yum -y update
echo "redhat" | sudo passwd root --stdin
sudo sed -i 's/PasswordAuthentication no/PasswordAuthentication yes/' /etc/ssh/sshd_config
yum -y install docker
systemctl enable docker
systemctl start docker
# Set SELinux in permissive mode (effectively disabling it)
sudo setenforce 0
sudo sed -i 's/^SELINUX=enforcing$/SELINUX=permissive/' /etc/selinux/config
SCRIPT

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  
  if Vagrant.has_plugin?("vagrant-cachier")
    config.cache.scope = :box
  end

  config.hostmanager.enabled = true
  config.hostmanager.manage_host = true
  config.hostmanager.ignore_private_ip = false

  config.landrush.enabled = true
  config.landrush.tld = 'dev.com'
  config.landrush.guest_redirect_dns = false 

  config.vm.provider "virtualbox" do |v|
     v.memory = 1500
     v.cpus = 1
  end
 
  config.vm.define :master do |master|
    master.vm.box = centos_box_name
    #master.vm.box_url = centos_box_url
    master.vm.network :private_network, ip: "#{NETWORK_BASE}.#{INTEGRATION_START_SEGMENT}"
    master.vm.hostname = "master.dev.com"
    master.vm.network "forwarded_port", guest: 6443, host: 6443, host_ip: "127.0.0.1"
    master.vm.network "forwarded_port", guest: 7777, host: 7777, host_ip: "127.0.0.1"
    auto_correct = true
  end

config.vm.provision "shell", inline: $miscellany

end
