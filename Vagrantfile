# -*- mode: ruby -*-
# vi: set ft=ruby :

# Vagrantfile API/syntax version. Don't touch unless you know what you're doing!
VAGRANTFILE_API_VERSION = '2'

domain      = 'vagrant'
environment = 'vagrant'
vm_network  = '192.168.11'

vagrant_hosts = [
  { # Jenkins #1
    :ip      => "#{vm_network}.11",
    :host    => 'vgr-jen01',
    :os      => 'ubuntu',
    :version => 'trusty64',
    :mem     => '2048',
  },
]
  
Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.ssh.shell        = "bash -c 'BASH_ENV=/etc/profile exec bash'"
  config.ssh.insert_key   = false
  config.landrush.enabled = true
  config.landrush.tld     = domain

  vagrant_hosts.each do |server|
    config.vm.define server[:host].to_sym do |host|
      host.vm.box      = "boxes/#{server[:os]}/#{server[:version]}.box"
      host.vm.hostname = "#{server[:host]}.#{domain}"
      host.vm.network  :public_network, :nic_type => 'Am79C973', :ip => server[:ip], :bridge => 'eth0'
      host.vm.network  'forwarded_port', guest: 8080, host: 8080
      host.vm.network  'forwarded_port', guest: 8081, host: 8081

      host.vm.provider 'virtualbox' do |vb|
        vb.customize ['modifyvm', :id, '--memory', server[:mem]]
        vb.customize ['modifyvm', :id, '--natdnshostresolver1', 'on']
        vb.name = server[:host]
      end

      host.vm.provision 'ansible' do |ansible|
        ansible.playbook = 'site.yml'
        ansible.sudo     = true
      end
    end
  end
end
