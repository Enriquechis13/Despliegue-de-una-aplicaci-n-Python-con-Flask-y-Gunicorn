Vagrant.configure("2") do |config|
  # Máquina virtual flask
  config.vm.define "flask" do |flask|
    flask.vm.box = "debian/bullseye64"
    flask.vm.hostname = "flask.sistema.test"
    flask.vm.network "private_network", ip: "192.168.57.102"
    flask.vm.synced_folder "./python-flask-gunicorn-html", "/home/vagrant/python-flask-gunicorn-html"

  end
end
