# -*- mode: ruby -*-
# vi: set ft=ruby :

# Vagrantfile API/syntax version. Don't touch unless you know what you're doing!
VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|

    num_minions = 2
    tasks = [
        "001-installation",
        "002-modules",
        "003-grains",
        "004-states",
        "005-highstate",
        "006-templating",
        "007-pillars",
        "008-templatedstates",
        "101-orchestration"]
    task_id = 0

    def configure_vbox_provider(config, name, cpu=1, memory=1024)
        config.vm.provider :virtualbox do |vb|
            vb.memory = "#{memory}"
            vb.cpus = cpu
            vb.name = "#{name}"
        end
    end
    def configure_hyperv_provider(config, name, cpu=1, memory=1024)
        config.vm.provider :hyperv do |hv|
            hv.memory = "#{memory}"
            hv.cpus = cpu
            hv.vmname = "#{name}"
        end
    end

    
    
    vm_name_master = "master"
    config.vm.define "#{vm_name_master}", primary: true do |master_config|

        configure_vbox_provider(master_config, vm_name_master)
        configure_hyperv_provider(master_config, vm_name_master)

        master_config.vm.box = "generic/ubuntu1604"
        master_config.vm.host_name = "master.local"

        master_config.vm.synced_folder "#{tasks[task_id]}/salt/", "/srv/salt"
        master_config.vm.synced_folder "#{tasks[task_id]}/pillar/", "/srv/pillar"

        master_config.vm.provision :salt do |salt|
            salt.install_master = true
            salt.no_minion = true
        end

    end

    (1..num_minions).each do |i|

        vm_name_minion = "minion#{('%02d' % i)}"
        config.vm.define "#{vm_name_minion}" do |minion_config|

            configure_vbox_provider(minion_config, vm_name_minion)
            configure_hyperv_provider(minion_config, vm_name_minion)

            minion_config.vm.box = i.even? ? "generic/ubuntu1604" : "generic/centos7"
            minion_config.vm.host_name = "#{vm_name_minion}.local"

            minion_config.vm.provision :salt do |salt|
                salt.install_master = false
            end
        end
    end
end
