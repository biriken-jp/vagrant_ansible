# -*- mode: ruby -*-
# vi: set ft=ruby :
require 'yaml'
VAGRANTFILE_API_VERSION = "2"


Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  # plugin install
  if Vagrant.has_plugin?('vagrant-hostsupdater') === false
    system("vagrant plugin install vagrant-hostsupdater")
  end
  if Vagrant.has_plugin?('vagrant-vbguest') === false
    system("vagrant plugin install vagrant-vbguest")
  end

  # require config
  _conf = YAML.load(
      File.open(
          File.join(File.dirname(__FILE__), 'vagrant.yml'),
          File::RDONLY
      ).read
  )

  # setting plugin
  if Vagrant.has_plugin?('vagrant-hostsupdater')
    config.hostsupdater.remove_on_suspend = true
  end
  if Vagrant.has_plugin?('vagrant-vbguest')
    config.vbguest.auto_update = true
  end

  # setting hostname and memory
  config.vm.provider :virtualbox do |vb|
    vb.name = _conf["hostname"]
    vb.memory = _conf["memory"].to_i
  end

  # setting box
  config.vm.box = _conf['box']
  # setting ip
  config.vm.network :private_network, ip: _conf['ip']
  # setting synced folder
  config.vm.synced_folder _conf["synced_folder"], _conf["document_root"], create: true, owner: 'vagrant', group: 'vagrant', mount_options: ['dmode=777,fmode=666']

  # make ansible/hosts
  File.open("ansible/hosts", "w") do |f|
    f.puts("[localhost]\r\n")
    f.puts(_conf['ip'])
  end

  # run ansible
  config.vm.provision "ansible" do |ansible|
    ansible.playbook = "ansible/install.yml"
    ansible.inventory_path = "ansible/hosts"
    ansible.verbose = true
    ansible.sudo = true
    ansible.limit = "all"
  end
end
