VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
    config.vm.box = "ubuntu/trusty64"
    config.vm.network :private_network, ip: "192.168.33.33"
    config.vm.synced_folder "sites/", "/var/www/", create: true, mount_options: ['dmode=777', 'fmode=776']
    config.vm.network :forwarded_port, host: 1080, guest: 1080

    if Vagrant.has_plugin? 'vagrant-hostmanager'
      config.hostmanager.enabled = true
      config.hostmanager.manage_host = true
      config.hostmanager.manage_guest = true
      config.hostmanager.ignore_private_ip = false
      config.hostmanager.include_offline = true
      config.vm.define 'dev-box' do |node|
        node.hostmanager.aliases = %w(ccms-local.carleton.ca, framework-local.carleton.ca, kb-local.carleton.ca, intranet.carleton.ca)
      end
    else
         puts 'vagrant-hostsupdater missing, please install the plugin:'
         puts 'vagrant plugin install vagrant-hostmanager'
    end

    config.vm.provision :ansible do |ansible|
        ansible.playbook = "ansible/playbook.yml"
    end

    config.vm.provider :virtualbox do |v|
        v.name = "dev-box"
        v.customize [
            "modifyvm", :id,
            "--name", "dev-box",
            "--memory", 512,
            "--natdnshostresolver1", "on",
            "--cpus", 1,
        ]
    end

end
