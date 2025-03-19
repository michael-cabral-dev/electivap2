Vagrant.configure("2") do |config|
    # Configuración general para las máquinas virtuales
    config.vm.provider "virtualbox" do |vb|
      vb.memory = "512"
      vb.cpus = 1
    end
  
    # Servidor Web 1 (Apache)
    config.vm.define "web1" do |web1|
      web1.vm.box = "ubuntu/bionic64"
      web1.vm.network "private_network", ip: "192.168.33.11"
      web1.vm.synced_folder "./web1", "/var/www/html"
      web1.vm.provision "shell", inline: <<-SHELL
        sudo apt-get update
        sudo apt-get install -y apache2
        sudo systemctl enable apache2
        sudo systemctl start apache2
        echo '<h1>Servidor Web 1</h1>' | sudo tee /var/www/html/index.html
      SHELL
    end
  
    # Servidor Web 2 (Apache)
    config.vm.define "web2" do |web2|
      web2.vm.box = "ubuntu/bionic64"
      web2.vm.network "private_network", ip: "192.168.33.12"
      web2.vm.synced_folder "./web2", "/var/www/html"
      web2.vm.provision "shell", inline: <<-SHELL
        sudo apt-get update
        sudo apt-get install -y apache2
        sudo systemctl enable apache2
        sudo systemctl start apache2
        echo '<h1>Servidor Web 2</h1>' | sudo tee /var/www/html/index.html
      SHELL
    end
  
    # Servidor Nginx (Balanceador de carga)
    config.vm.define "nginx" do |nginx|
      nginx.vm.box = "ubuntu/bionic64"
      nginx.vm.network "private_network", ip: "192.168.33.10"
      nginx.vm.provision "shell", inline: <<-SHELL
        sudo apt-get update
        sudo apt-get install -y nginx
        sudo systemctl enable nginx
        sudo systemctl start nginx
  
        # Configurar Nginx como balanceador de carga
        cat <<EOL | sudo tee /etc/nginx/sites-available/load_balancer
        upstream backend {
            server 192.168.33.11;
            server 192.168.33.12;
        }
  
        server {
            listen 80;
            location / {
                proxy_pass http://backend;
            }
        }
        EOL
  
        # Activar configuración y reiniciar Nginx
        sudo ln -sf /etc/nginx/sites-available/load_balancer /etc/nginx/sites-enabled/
        sudo systemctl restart nginx
      SHELL
    end
  end
  