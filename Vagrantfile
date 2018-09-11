# = Vagrantfile
#
#
# -*- mode: ruby -*-
# vi: set ft=ruby :
#
#
VAGRANTFILE_API_VERSION = "2"
Vagrant.require_version ">= 2.0.1" # WinRM issue #9204 fixed w/ Vagrant 2.0.1 (https://github.com/hashicorp/vagrant/issues/9204)


# == Plugins
#
# Doc:
# - https://www.vagrantup.com/docs/plugins/usage.html
#
# Plugins can be installed from:
# * a known gem source
# * a local file source
#
plugins = {
  # Name => SOURCE
  'winrm' => '',
  'winrm-fs' => '',
  'vagrant-vbguest' => '',
  'vagrant-vbguest-windows' => './plugins/vagrant-vbguest-windows-0.1.0.gem'
}

plugins.each_pair do |plugin_name, plugin_path|
  unless Vagrant.has_plugin?(plugin_name)
    # Attempt to install ourself. Bail out on failure so we don't get stuck in an
    # infinite loop.
    system("vagrant plugin install #{(plugin_path != '' ? plugin_path : plugin_name)}") || exit!

    # Relaunch Vagrant so the plugin is detected. Exit with the same status code.
    exit system('vagrant', *ARGV)
  end
end



# == Vagrant VM config
#
Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|

  # https://app.vagrantup.com/gusztavvargadr/boxes/w10e
  # Size ~6Go
  config.vm.box = "gusztavvargadr/w10e"

  config.vm.boot_timeout = 600
  config.vm.graceful_halt_timeout = 600

  config.vm.guest = :windows
  config.vm.communicator = "winrm"
  config.winrm.username = "vagrant"
  config.winrm.password = "vagrant"


  # === Connection
  #
  # RDP
  config.vm.network :forwarded_port, guest: 3389, host: 3389
  # WinRM HTTP !!
  config.vm.network :forwarded_port, guest: 5985, host: 5985, id: "winrm", auto_correct: true


  # === System config
  #
  config.vm.provider "virtualbox" do |vm|
      vm.name = "Windows10"
      vm.gui = true
      vm.cpus = 1
      vm.memory = 4096
      vm.linked_clone = false
  end


  # == Shared Folders
  #
  config.vm.synced_folder File.absolute_path(File.join(ENV['HOME'], 'Documents')), "/HOST_Documents", create: true
end