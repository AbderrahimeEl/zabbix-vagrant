Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/jammy64"                                          # Ubuntu 22.04 LTS
  config.vm.network "forwarded_port", guest: 80, host: 8080                 # Frontend HTTP
  config.vm.network "forwarded_port", guest: 10051, host: 10051             # Zabbix server
  config.vm.provider "virtualbox" do |vb|
    vb.memory = 2048                                                        # 2 GB RAM
  end

  # Install Docker Engine using the Docker provisioner
  config.vm.provision "docker"                                              # Automatically installs Docker
end
