# -*- mode: ruby -*-
# vi: set ft=ruby :

VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.omnibus.chef_version = :latest
  config.vm.box = "precise64"
  config.vm.box_url = "http://files.vagrantup.com/precise64.box"
  config.vm.boot_timeout = 300

  config.vm :virtualbox do |v|
    v.memory = 1024
    v.cpus = 1
  end

  # Configure Redis VM
  config.vm.define "db_redis" do |db_redis|
    db_redis.vm.network "private_network", ip: "192.168.2.51"

    db_redis.vm.provision :chef_solo do |chef|
      chef.cookbooks_path = File.join(File.dirname(__FILE__), 'cookbooks')
      chef.add_recipe "apt"
      chef.add_recipe "build-essential"
      chef.add_recipe "redis"
    end
  end

  # Configure PostgreSQL VM
  config.vm.define "db_postgres" do |db_postgres|
    db_postgres.vm.network "private_network", ip: "192.168.2.50"

    db_postgres.vm.provision :chef_solo do |chef|
      chef.cookbooks_path = File.join(File.dirname(__FILE__), 'cookbooks')
      chef.add_recipe "apt"
      chef.add_recipe "postgresql::server"
      chef.add_recipe "phppgadmin"
      chef.add_recipe "build-essential"

      chef.json = {
        :postgresql => {
          :version => "9.1",
          :listen_address => "*",
          :pg_hba => [
            "host all all 0.0.0.0/0 md5",
            "host all all ::1/0 md5"
          ],
          :users => [
            {
              :username => "postgres",
              :password => "password",
              :superuser => true,
              :login => true,
              :creatdb => true,
            },
            {
              :username => "test",
              :password => "password",
              :superuser => false,
              :login => true,
              :createdb => true,
            }
          ],
          
          # set postgresql.conf attributes
          :log_statement => "all",
          :log_destination => "stderr",
          :logging_collector => "on",
          :log_directory => "/var/log/postgresql",
          :log_filename => "statement_logs",
          :log_min_messages => "info",
          :log_min_error_statement => "info"
        },
      }
    end
  end
end

