# -*- mode: ruby -*-
# vi: set ft=ruby :
VAGRANTFILE_API_VERSION = "2"

require 'yaml'
configuration = YAML::load_file('vagrant/vagrant.yml')

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.box = configuration['box']

  configuration['hosts'].each do |host|
    config.vm.define host['name'] do |h|
      h.vm.network 'forwarded_port', guest: host['port'], host: host['port']

      h.vm.provider 'virtualbox' do |vb|
        vb.customize ['modifyvm', :id, '--memory', host['memory']]
        vb.customize ['modifyvm', :id, '--cpus', host['cpus']]
      end

      h.vm.provision 'shell', inline: configuration['shell']

      config.vm.provision 'ansible' do |ansible|
        ansible.playbook        = 'ansible/playbook.yml'
        ansible.limit           = 'all'
        ansible.verbose         = 'v'
        ansible.skip_tags       = 'aws'
        if ENV['ANSIBLE_START_AT']
          ansible.start_at_task = ENV['ANSIBLE_START_AT']
        end
      end
    end
  end
end
