require 'socket'

hostname = Socket.gethostname
localmachineip = IPSocket.getaddress(Socket.gethostname)
puts %Q{ This machine has the IP '#{localmachineip} and host name '#{hostname}'}

# Vagrantfile API/syntax version.
VAGRANTFILE_API_VERSION = '2'
#variables par défaut
centos_box_name = 'eurolinux-vagrant/centos-stream-9'
NETWORK_BASE = '192.168.56'
INTEGRATION_START_SEGMENT = 24

$miscellany = <<SCRIPT
yum -y install wget git net-tools bind-utils iptables-services bridge-utils bash-completion kexec-tools sos psacct nfs-utils
#sudo cp /vagrant/yum.repos /etc/yum.repos.d/open.repo
#sudo cp /vagrant/deploy.sh /home/vagrant/deploy.sh
yum clean all ; yum repolist
yum -y update
echo "redhat" | sudo passwd root --stdin
sudo sed -i 's/PasswordAuthentication no/PasswordAuthentication yes/' /etc/ssh/sshd_config
sudo yum install -y yum-utils
sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
sudo yum -y install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
#sudo service sshd restart
#cat <<EOF | sudo tee /etc/yum.repos.d/kubernetes.repo
#[kubernetes]
#name=Kubernetes
#baseurl=https://packages.cloud.google.com/yum/repos/kubernetes-el7-x86_64
#enabled=1
#gpgcheck=1
#repo_gpgcheck=1
#gpgkey=https://packages.cloud.google.com/yum/doc/yum-key.gpg https://packages.cloud.google.com/yum/doc/rpm-package-key.gpg
#exclude=kubelet kubeadm kubectl
#EOF
cat <<EOF | sudo tee /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=https://pkgs.k8s.io/core:/stable:/v1.30/rpm/
enabled=1
gpgcheck=1
gpgkey=https://pkgs.k8s.io/core:/stable:/v1.30/rpm/repodata/repomd.xml.key
exclude=kubelet kubeadm kubectl cri-tools kubernetes-cni
EOF
# Set SELinux in permissive mode (effectively disabling it)
sudo setenforce 0
sudo sed -i 's/^SELINUX=enforcing$/SELINUX=permissive/' /etc/selinux/config
sudo yum install -y kubelet kubeadm kubectl --disableexcludes=kubernetes
sudo systemctl enable --now kubelet
cat <<EOF >  /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF
sysctl --system
echo 1 > /proc/sys/net/ipv4/ip_forward
swapoff -a
SCRIPT

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  
  if Vagrant.has_plugin?("vagrant-cachier")
    config.cache.scope = :box
  end

  config.hostmanager.enabled = true
  config.hostmanager.manage_host = true
  config.hostmanager.ignore_private_ip = false
  config.hostmanager.include_offline = true
  config.hostmanager.manage_guest = true

  config.landrush.enabled = true
  config.landrush.tld = 'dev.com'
  config.landrush.guest_redirect_dns = false 
  config.vbguest.auto_update = false

  config.vm.provider "virtualbox" do |v|
     v.memory = 1500
     v.cpus = 3
  end
 
 # config.vm.define :nfs do |nfshost|
 #   nfshost.vm.box = centos_box_name
 #   nfshost.vm.network :private_network, ip: "#{NETWORK_BASE}.#{INTEGRATION_START_SEGMENT + 4}"
 #   nfshost.hostmanager.aliases = %w(nfs.dev.com)
 #   nfshost.vm.hostname = "nfs.dev.com"
 # end

  config.vm.define :master do |master|
    master.vm.box = centos_box_name
    master.vm.network :private_network, ip: "#{NETWORK_BASE}.#{INTEGRATION_START_SEGMENT}"
    master.vm.hostname = "master.dev.com"
    master.vm.provision "hostmanager"
   # master.vm.network "forwarded_port", guest: 7777, host: 7777, host_ip: "127.0.0.1"
    auto_correct = true
  end

  config.vm.define :node1 do |node|
    node.vm.box = centos_box_name
    #node.vm.box_url = centos_box_url
    node.vm.network :private_network, ip: "#{NETWORK_BASE}.#{INTEGRATION_START_SEGMENT + 1}"
    node.vm.hostname = "worker1.dev.com"
  end
 
  config.vm.define :node2 do |node|
    node.vm.box = centos_box_name
    #node.vm.box_url = centos_box_url
    node.vm.network :private_network, ip: "#{NETWORK_BASE}.#{INTEGRATION_START_SEGMENT + 2}"
    node.vm.hostname = "worker2.dev.com"
    auto_correct = true
  end

config.vm.provision "shell", inline: $miscellany

end
