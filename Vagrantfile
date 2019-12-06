# -*- mode: ruby -*-
# vi: set ft=ruby :

ENV['VAGRANT_DEFAULT_PROVIDER'] = 'libvirt'

# check required plugins
REQUIRED_PLUGINS_LIBVIRT = %w(vagrant-libvirt)
exit unless REQUIRED_PLUGINS_LIBVIRT.all? do |plugin|
  Vagrant.has_plugin?(plugin) || (
    puts "The #{plugin} plugin is required. Please install it with:"
    puts "$ vagrant plugin install #{plugin}"
    false
  )
end

# Defaults for config options defined in CONFIG
$vm_name = "taskd"
$vm_os = "debian/stretch64"
$vm_cpus = 1
$vm_memory = 1024
#$vm_ip = "192.168.69.69"

 

Vagrant.configure("2") do |config|

  # Taskd Server
  config.vm.define :taskd do |node|
    node.vm.hostname = "#{$vm_name}"
    node.vm.box = "#{$vm_os}"
    node.vm.box_check_update = false
    node.vm.synced_folder ".", "/vagrant"

#    node.vm.network "private_network", ip: "#{$vm_ip}"

    node.vm.provider :libvirt do |lb|
      lb.uri = "qemu:///system"
      lb.host = "#{$vm_name}"
      lb.cpus = "#{$vm_cpus}"
      lb.memory = "#{$vm_memory}"
      lb.nested = true
    end

    node.vm.provider :virtualbox do |vb|
      vb.name = "#{$vm_name}"
      vb.cpus = "#{$vm_cpus}"
      vb.memory = "#{$vm_memory}"
    end

  end

  config.vm.provision :ansible_local do |ansible|
    ansible.verbose = "vv"
    ansible.playbook = "tests/vagrant.yml"
    ansible.install_mode = "pip"
    ansible.extra_vars = {
    taskd_organizations: ["OBS"],
    taskd_users: [
      {
        name: "laurent_g",
        organization: "OBS"
      },
      {
        name: "First Last",
        organization: "Public"
      },
    ],
    taskd_selfsigned_organization: "OBS",
    taskd_selfsigned_country: "FR",
    taskd_selfsigned_state: "AQ",
    taskd_selfsigned_locality: "Bx",
    taskd_taskwarrior_user: "vagrant",
    taskd_selfsigned_clients_download: true,
    taskd_selfsigned_clients_download_dir: "~/.taskd/",
    taskd_server: "#{$vm_name}",
    taskd_taskwarrior_config: true,
    taskd_taskwarrior_config_path: "~/.taskd/taskd.rc",
    taskd_android_config: true
    }
  end

end
