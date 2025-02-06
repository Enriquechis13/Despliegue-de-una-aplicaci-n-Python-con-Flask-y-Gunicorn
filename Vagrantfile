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
      pip3 install virtualenv
    virtualenv /home/vagrant/.local/share/virtualenvs/emu_env
    source /home/vagrant/.local/share/virtualenvs/emu_env/bin/activate
    pip install --upgrade pip
    cd /var/www/emunrodb
    pip install pipenv
    pipenv install
    pipenv install gunicorn
    sudo apt install nginx
    gunicorn --workers 3 --bind unix:/var/www/emunrodb/emunrodb.sock wsgi:app &
    echo '
    server {
      listen 80;
      server_name app.izv www.app.izv;

      access_log /var/log/nginx/app.access.log;
      error_log /var/log/nginx/app.error.log;

      location / {
        include proxy_params;
        proxy_pass http://unix:/var/www/emunrodb/emunrodb.sock;
      }
    }' > /etc/nginx/sites-available/emunrodb
    ln -s /etc/nginx/sites-available/emunrodb /etc/nginx/sites-enabled
    systemctl restart nginx
    SHELL
  end
end

# Toma nota de la ruta pues nos hará falta más adelante para crear el servicio de systemd para arrancar nuestra aplicación.
#/usr/bin/gunicorn

# Y un último detalle, Gunicorn debe iniciarse ahora así:
# gunicorn --workers 4 --bind 0.0.0.0:5000 wsgi:app