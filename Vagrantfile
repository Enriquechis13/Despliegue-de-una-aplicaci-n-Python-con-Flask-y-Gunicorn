Vagrant.configure("2") do |config|
  # Máquina virtual flask
  config.vm.define "flask" do |flask|
    flask.vm.box = "debian/bullseye64"
    flask.vm.hostname = "flask.sistema.test"
    flask.vm.network "private_network", ip: "192.168.57.102"
    flask.vm.synced_folder "./python-flask-gunicorn-html", "/home/vagrant/python-flask-gunicorn-html"

    flask.vm.provision "shell", privileged: false, inline: <<-SHELL
      sudo apt-get update
      sudo apt-get install -y python3-pip
      pip3 install --user pipenv python-dotenv
      sudo mkdir -p /var/www/emunrodb
      sudo chown -R $USER:www-data /var/www/emunrodb
      sudo chmod -R 775 /var/www/emunrodb
      sudo nano /var/www/emunrodb/.env
      sudo touch /var/www/emunrodb/.env
    SHELL
  end
end
