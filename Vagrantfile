# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure(2) do |config|

  config.vm.box = "ubuntu/xenial64"

  config.vm.network "forwarded_port", guest: 3000, host: 3000
  config.vm.network :forwarded_port, guest: 80, host: 8080
  config.vm.network :forwarded_port, guest: 3306, host: 8888

  config.vm.provider "virtualbox" do |vb|
    vb.memory = "1024"
  end


  config.vm.provision "shell", inline: <<-SHELL
    echo "Updating package definitions"
    sudo apt-get update

    echo "Installing git and build tools"
    sudo apt-get -y install git-core curl zlib1g-dev build-essential libssl-dev libreadline-dev libyaml-dev libsqlite3-dev sqlite3 libxml2-dev libxslt1-dev libcurl4-openssl-dev python-software-properties libffi-dev  
  SHELL

  config.vm.provision "shell", privileged: false, inline: <<-SHELL
    if [ ! -d "$HOME/.rbenv" ]; then
      echo "Installing rbenv and ruby-build"

      git clone https://github.com/sstephenson/rbenv.git ~/.rbenv
      git clone https://github.com/sstephenson/ruby-build.git ~/.rbenv/plugins/ruby-build

      echo ‘Adding rbenv to path’
      echo 'export PATH="$HOME/.rbenv/bin:$PATH"' >> ~/.bashrc
      echo 'eval "$(rbenv init -)"' >> ~/.bashrc

      git clone https://github.com/rbenv/rbenv-gem-rehash.git ~/.rbenv/plugins/rbenv-gem-rehash
    else
      echo "Updating rbenv and ruby-build"

      cd ~/.rbenv
      git pull

      cd ~/.rbenv/plugins/ruby-build
      git pull
    fi

    export PATH="$HOME/.rbenv/bin:$PATH"
    eval "$(rbenv init -)"

    if [ ! -d "$HOME/.rbenv/versions/2.3.1" ]; then
      echo "Installing ruby"

      rbenv install 2.3.1
      rbenv global 2.3.1

      gem update --system
      gem update

      gem install bundler
      bundle config path vendor/bundle

      rbenv rehash

      curl -sL https://deb.nodesource.com/setup_4.x | sudo -E bash -
      sudo apt-get install -y nodejs
      
      gem install rails —-pre
      rbenv rehash

      debconf-set-selections <<< 'mysql-server mysql-server/root_password password 123456’
      debconf-set-selections <<< 'mysql-server mysql-server/root_password_again password 123456’
      sudo apt-get -y install mysql-server mysql-client libmysqlclient-dev
    fi
  SHELL

end
