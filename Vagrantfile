Vagrant.configure("2") do |config|
  # Máquina virtual flask
  config.vm.define "flask" do |flask|
    flask.vm.box = "debian/bullseye64"
    flask.vm.hostname = "flask.sistema.test"
    flask.vm.network "private_network", ip: "192.168.57.102"
    flask.vm.synced_folder "./python-flask-gunicorn-html", "/home/vagrant/python-flask-gunicorn-html"

    flask.vm.provision "shell", privileged: false, inline: <<-SHELL
    sudo apt-get update && sudo apt-get install -y python3-pip nginx
    pip3 install pipenv python-dotenv

    sudo mkdir -p /var/www/emunrodb
    sudo chown -R vagrant:www-data /var/www/emunrodb
    sudo chmod -R 775 /var/www/emunrodb
    cd /var/www/emunrodb

    pipenv install flask gunicorn
    echo "export FLASK_APP=application.py" >> ~/.bashrc
    echo "export FLASK_ENV=production" >> ~/.bashrc

    echo "from flask import Flask
app = Flask(__name__)
@app.route('/')
def index():
    return '<h1>App desplegada</h1>'" > application.py
    
    echo "from application import app
if __name__ == '__main__':
    app.run(debug=False)" > wsgi.py
    
    echo "[Unit]
Description=flask app service - App con Flask y Gunicorn
After=network.target

[Service]
User=vagrant
Group=vagrant
Environment=\"PATH=/home/vagrant/.local/share/virtualenvs/vagrant-G6U0N2py/bin\"
WorkingDirectory=/var/www/emunrodb
ExecStart=/home/vagrant/.local/share/virtualenvs/vagrant-G6U0N2py/bin/gunicorn --workers 3 --bind unix:/var/www/emunrodb/emunrodb.sock wsgi:app

[Install]
WantedBy=multi-user.target" | sudo tee /etc/systemd/system/flask_app.service
    
    sudo apt install nginx
    sudo systemctl daemon-reload
    sudo systemctl enable flask_app
    sudo systemctl start flask_app
    
    echo "server {
    listen 80;
    server_name app.izv www.app.izv;

    access_log /var/log/nginx/app.access.log;
    error_log /var/log/nginx/app.error.log;

    location / {
        include proxy_params;
        proxy_pass http://unix:/var/www/emunrodb/emunrodb.sock;
    }
}" | sudo tee /etc/nginx/sites-available/emunrodb.conf
    
    sudo ln -s /etc/nginx/sites-available/emunrodb.conf /etc/nginx/sites-enabled/
    sudo nginx -t
    sudo systemctl restart nginx

    # Desmarcar los comentarios para automatizar la tarea de ampliación.

    # cd /var/www/
    # sudo apt install git
    # git clone https://github.com/Azure-Samples/msdocs-python-flask-webapp-quickstart
    # cd msdocs-python-flask-webapp-quickstart

    # pipenv shell
    # pipenv install -r requirements.txt

    # gunicorn --workers 4 --bind 0.0.0.0:5000 wsgi:app
    SHELL
  end
end