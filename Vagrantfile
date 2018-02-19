# -*- mode: ruby -*-
# vi: set ft=ruby :

VAGRANTFILE_API_VERSION = "2"

$MASTER_RUN = <<SCRIPT
#!/bin/bash
export DEBIAN_FRONTEND=noninteractive
/usr/bin/wget -q -O /tmp/puppetlabs-release-pc1-stretch.deb https://apt.puppetlabs.com/puppetlabs-release-pc1-stretch.deb
/usr/bin/dpkg -i /tmp/puppetlabs-release-pc1-stretch.deb
/bin/rm /tmp/puppetlabs-release-pc1-stretch.deb
/usr/bin/apt-get update
/usr/bin/apt-get -y install puppet-agent
/bin/ln -s /opt/puppetlabs/bin/puppet /usr/bin/puppet
/usr/sbin/useradd -r puppet
mkdir -p /etc/puppetlabs/code
/bin/cat <<EOF > /etc/puppetlabs/code/hiera.yaml
---
:hierarchy:
    - "nodes/%{::fqdn}"
    - default

:backends:
    - yaml
:yaml:
    :datadir: "/etc/puppetlabs/code/environments/%{environment}/hiera"
EOF
mkdir -p /etc/puppetlabs/puppet
/bin/cat <<EOF > /etc/puppetlabs/puppet/fileserver.conf
[production]
    path /etc/puppetlabs/code/environments/production/files
    allow *
EOF
puppet master --daemonize
exit 0
SCRIPT

$NODE_RUN = <<SCRIPT
#!/bin/bash
export DEBIAN_FRONTEND=noninteractive
/usr/bin/wget -q -O /tmp/puppetlabs-release-pc1-stretch.deb https://apt.puppetlabs.com/puppetlabs-release-pc1-stretch.deb
/usr/bin/dpkg -i /tmp/puppetlabs-release-pc1-stretch.deb
/bin/rm /tmp/puppetlabs-release-pc1-stretch.deb
/usr/bin/apt-get update
/usr/bin/apt-get -y install puppet-agent lsb-release apt-transport-https
/bin/ln -s /opt/puppetlabs/bin/puppet /usr/bin/puppet
exit 0
SCRIPT


Vagrant.configure("2") do |config|
  config.vbguest.auto_update = false
  config.hostmanager.enabled = true
  config.hostmanager.manage_guest = true
  config.hostmanager.ignore_private_ip = false

  config.vm.define "master" do |master|
    master.vm.box = "debian/stretch64"
    master.vm.hostname = "master.local"
    master.vm.network :private_network, ip: "192.168.14.100"

    master.vm.provision "shell", inline: $MASTER_RUN

    master.vm.provider "virtualbox" do |v|
      v.memory = 1024
      v.cpus = 2
    end
    master.vm.provider "vmware_fusion" do |v|
      v.vmx["memsize"] = "1024"
      v.vmx["numvcpus"] = "2"
    end

    master.vm.synced_folder ".", "/etc/puppetlabs/code/environments/production", type: "rsync", rsync__exclude: ".git/,.tmp/,.vagrant/,.librarian/", rsync__args: ["--verbose", "--rsync-path='sudo rsync'", "--archive", "--delete", "-z"]
  end

  config.vm.define "node" do |node|
    node.vm.box = "debian/stretch64"
    node.vm.hostname = "node.local"
    node.vm.network :private_network, ip: "192.168.14.101"
    
    node.vm.provision "shell", inline: $NODE_RUN

    node.vm.provider "virtualbox" do |v|
      v.memory = 1024
      v.cpus = 2
    end
    node.vm.provider "vmware_fusion" do |v|
      v.vmx["memsize"] = "1024"
      v.vmx["numvcpus"] = "2"
    end
  end
end
