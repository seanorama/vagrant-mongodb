# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure('2') do |config|
  config.vm.box     = 'dummy'

  config.berkshelf.enabled    = true
  config.omnibus.chef_version = :latest

  # Workaround for "sudo: sorry, you must have a tty to run sudo" error. See
  # https://github.com/mitchellh/vagrant/issues/1482 for details.
  config.ssh.pty = true

  config.vm.provider :rackspace do |rs, override|
    override.vm.box = 'dummy'
    override.vm.box_url = 'https://github.com/mitchellh/vagrant-rackspace/raw/master/dummy.box'
    rs.username = ENV['OS_USERNAME']
    rs.api_key  = ENV['OS_API_KEY']
    rs.rackspace_region = ENV.fetch('OS_REGION_NAME', :lon).downcase.to_sym
    rs.public_key_path = File.expand_path("~/.ssh/id_rsa.pub")
    #rs.key_name = 
    override.ssh.username = 'root'
    override.ssh.private_key_path = ENV['VAGRANT_SSH_PRIVATE_KEY_PATH'] ||
        "~/.ssh/id_rsa"
    rs.flavor   = /2 GB Performance/
    rs.image    = /CentOS 6.5/
    rs.disk_config = 'MANUAL'
#    rs.metadata = {
#      'expire-on' => (Date.today + 30).to_s
#    }
  end

  # See http://docs.mongodb.org/manual/administration/production-notes/ for
  # details.
  config.vm.provision :chef_solo do |chef|
    chef.add_recipe 'yum'
    chef.add_recipe 'utils'
    chef.add_recipe 'mosh'
    chef.add_recipe 'mongodb::10gen_repo'
    chef.add_recipe 'mongodb'

    if ENV['VAGRANT_DEBUG']
      chef.log_level = :debug
    end

    chef.json = {
      :mongodb => {
        :dbpath     => '/data',
        :smallfiles => true      # Speed up initial journal preallocation
      }
    }

  end
end
