# CONTAINER

This repository contains the template to generate the containers

------------------

# UBUNTU
sudo adduser evigra
sudo usermod -aG sudo evigra 


sudo apt-get install docker-compose
sudo apt-get install docker-compose-v2
sudo usermod -aG docker evigra
mkdir /home/evigra/.ssh/
mkdir /home/evigra/docker/
cd /home/evigra/.ssh/
nano /home/evigra/.ssh/e.vizcaino@solesgps.com
chmod 600 /home/evigra/.ssh/e.vizcaino@solesgps.com
chmod 700 /home/evigra/.ssh
sudo apt-get install git

sudo apt-get install nginx

sudo apt-get install certbot
sudo apt install python3-certbot-nginx



# GITHUB CONFIGURATION 


eval "$(ssh-agent -s)"; ssh-add ~/.ssh/e.vizcaino@solesgps.com
git remote set-url origin git@github.com:evigra/container_solesgps.git

git clone --recurse-submodules https://github.com/evigra/container_solesgps.git

git submodule add git@github.com:evigra/gpsmap.git addons/gpsmap
git submodule add git@github.com:evigra/gpsmap_company.git addons/gpsmap_company
git submodule add git@github.com:evigra/gpsmap_engine.git addons/gpsmap_engine
git submodule add git@github.com:evigra/gpsmap_geofence.git addons/gpsmap_geofence
git submodule add git@github.com:evigra/gpsmap_history.git addons/gpsmap_history
git submodule add git@github.com:evigra/gpsmap_mirror.git addons/gpsmap_mirror
git submodule add git@github.com:evigra/gpsmap_street.git addons/gpsmap_street
git submodule add git@github.com:evigra/gpsmap_website.git addons/gpsmap_website
git submodule add git@github.com:evigra/instance_solesgps.git addons/instance_solesgps




# DOCKER CONTAINER CONFIGURATIONS

sudo chown $(whoami):$(whoami) /var/run/docker.sock


# Create db container 
docker run --restart unless-stopped -d -e POSTGRES_USER=odoo -e POSTGRES_PASSWORD=odoo -e POSTGRES_DB=postgres -p 5432:5432 --name db16 postgres:16

# Create web container 
docker run --restart unless-stopped -v ~/docker/container_solesgps/addons:/mnt/extra-addons -v ~/docker/container_solesgps/config:/etc/odoo -p 8016:8069 -p 8006:8006 --name container_solesgps --link db16:db -t odoo:16.0

docker run --restart unless-stopped -v ~/docker/container_solesgps/addons:/mnt/extra-addons -v ~/docker/container_solesgps/config:/etc/odoo -p 8016:8069 -p 8006:8006 --name container_solesgps --link db16:db -t odoo:16.0

# Create database
docker exec -ti container_solesgps bash -c "/usr/bin/odoo -p 8006  -d www16 --gevent-port=8672 --db_host db --db_port 5432  --db_user odoo --db_password odoo --without-demo all"

docker exec -ti container_solesgps bash -c "/usr/bin/odoo -p 8006  -d www16 --gevent-port=8072 --db_host db --db_port 5432  --db_user odoo --db_password odoo --without-demo all"


# Install modules
docker exec -ti container_solesgps16 bash -c "/usr/bin/odoo -i instance_solesgps -p 8006 -d www16 --gevent-port=8672 --db_host db --db_port 5432 --db_user odoo --db_password odoo --without-demo all"

docker exec -ti container_solesgps bash -c "/usr/bin/odoo -u instance_solesgps,gpsmap,gpsmap_engine -p 8006 -d container_solesgps --gevent-port=8672 --db_host db --db_port 5432 --db_user odoo --db_password odoo --without-demo all"

docker exec -it container_solesgps bash -c "odoo -d container_solesgps -u instance_solesgps -p 8006 --db_host=container_postgres_16 --db_port=5432 --db_user=odoo --db_password=odoo  --without-demo=all"


# show container in console
docker exec -it container_solesgps /bin/bash




# NGIX

sudo nano /etc/nginx/sites-enabled/default

sudo nginx -t

sudo systemctl restart nginx

sudo certbot --nginx -d solesgps.com -d www.solesgps.com

sudo certbot certificates

sudo systemctl status certbot.timer

sudo certbot renew --dry-run    

server {
    listen 80;
    server_name solesgps.com www.solesgps.com;
    client_max_body_size 10M;

    proxy_read_timeout 720s;
    proxy_connect_timeout 720s;
    proxy_send_timeout 720s;

    location / {
        proxy_pass http://127.0.0.1:8016;

        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;

        proxy_redirect off;
    }

    location /websocket {
        proxy_pass http://127.0.0.1:8016;

        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";

        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;

        proxy_read_timeout 720s;
    }
}
