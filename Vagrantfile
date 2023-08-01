Vagrant.configure("2") do |config|
  # Configuração da máquina virtual
  config.vm.box = "ubuntu/bionic64" # Imagem oficial do Ubuntu 18.04
  config.vm.network "private_network", ip: "192.168.33.10"

  # Disksize
  config.disksize.size = '40GB'

  # NOTE: This will enable public access to the opened port
  config.vm.network "forwarded_port", guest: 81, host: 81
  config.vm.network "forwarded_port", guest: 80, host: 80
  config.vm.network "forwarded_port", guest: 3306, host: 3306
  config.vm.network "forwarded_port", guest: 8080, host: 8080
  config.vm.network "forwarded_port", guest: 9000, host: 9000
  config.vm.network "forwarded_port", guest: 19999, host: 19999
  config.vm.network "forwarded_port", guest: 9001, host:9001

  config.vm.provider "virtualbox" do |vb|
    vb.memory = "2048" # Quantidade de memória RAM da máquina (em MB)
    vb.cpus = 2 # Número de CPUs da máquina
    #vb.gui = true
  end

  # Enable Dynamic Swap Space to prevent Out of Memory crashes
  config.vm.provision "shell", inline: "sudo apt-get update && sudo apt-get install swapspace python python3-pip python-dev libmysqlclient-dev python3-venv  build-essential libssl-dev libffi-dev unzip -y"

  # Configuração para permitir SSH para a máquina
  config.ssh.insert_key = false # Desabilita a geração de chave SSH automaticamente

  # Mysql Part
  $script_mysql = <<-SCRIPT
    sudo su && \
    apt-get update && \    
    apt-get install -y openjdk-8-jdk  mysql-server-5.7 && \
    mysql -e "DROP USER 'devops'@'localhost'" && \
    mysql -e "DROP USER 'devops_dev'@'localhost'" && \
    mysql -e "FLUSH PRIVILEGES;" && \
    mysql -e "create user 'devops'@'localhost' identified by 'mestre';"  && \
    mysql -e "create user 'devops_dev'@'localhost' identified by 'mestre';"  && \
    mysql -e "create database if not exists todo;" && \
    mysql -e "create database if not exists todo_dev;" && \
    mysql -e "create database if not exists test_todo_dev;" && \
    mysql -e "grant all privileges on *.* to devops@'localhost' identified by 'mestre';" && \
    mysql -e "grant all privileges on *.* to devops_dev@'localhost' identified by 'mestre';" && \
    mysql -e "FLUSH PRIVILEGES;"
  SCRIPT
  config.vm.provision "shell", inline: $script_mysql
  config.vm.provision "shell",
    inline: "cat /configs/mysqld.cnf > /etc/mysql/mysql.conf.d/mysqld.cnf"
  config.vm.provision "shell",
    inline: "service mysql restart"
  config.vm.synced_folder "./configs", "/configs"

  config.vm.provision "shell", path: "scripts/jenkins.sh"
  config.vm.provision "shell", path: "scripts/docker.sh"
end
