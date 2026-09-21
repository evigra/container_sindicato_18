# ubuntu
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

# container_sindicato_18

This repository contains the template to generate the containers

------------------

# GITHUB CONFIGURATION 


eval "$(ssh-agent -s)"; ssh-add ~/.ssh/e.vizcaino@solesgps.com
git remote set-url origin git@github.com:evigra/container_sindicato_18.git

git clone --recurse-submodules git@github.com:evigra/container_sindicato_18.git

git submodule add git@github.com:evigra/instance_sindicato.git addons/instance_sindicato


# DOCKER CONTAINER CONFIGURATIONS
sudo chown $(whoami):$(whoami) /var/run/docker.sock

# Instancia odoo
docker exec -it container_sindicato_18 bash -c "odoo -p 8008 --db_host=container_postgres_16 --db_port=5432 --db_user=odoo --db_password=odoo  --without-demo=all"

# Modules 
docker exec -it container_sindicato_18 bash -c "odoo -d container_sindicato_18 -i instance_sindicato -p 8008 --db_host=container_postgres_16 --db_port=5432 --db_user=odoo --db_password=odoo  --without-demo=all"

docker exec -it container_sindicato_18 bash -c "odoo -d container_sindicato_18 -u instance_sindicato -p 8008 --db_host=container_postgres_16 --db_port=5432 --db_user=odoo --db_password=odoo  --without-demo=all"


docker exec -it container_sindicato_18 odoo shell \
    -d container_sindicato_18 -p 8008 --db_host=container_postgres_16 --db_port=5432 --db_user=odoo \ --db_password=odoo

# Modules TEST
docker exec -it container_sindicato_18 bash -c "odoo -d container_sindicato_18_test -i instance_sindicato -p 8006--db_host=container_postgres_16 --db_port=5432 --db_user=odoo --db_password=odoo  --without-demo=all"


# show container in console
docker exec -it container_sindicato_18 /bin/bash

docker exec -it container_sindicato_18 bash -c "odoo shell -d container_sindicato_18 -u instance_sindicato -p 8008 --db_host=container_postgres_16 --db_port=5432 --db_user=odoo --db_password=odoo  --without-demo=all"


http://container_sindicato_18.localhost:8018
http://container_sindicato_18_test.localhost:8008

docker stop container_odoo db16
docker rm container_odoo db16 
clear

sudo chown evigra:evigra *



# NGIX

sudo nano /etc/nginx/sites-enabled/default

sudo nginx -t

sudo systemctl restart nginx

sudo certbot --nginx -d sntss-xxv.com -d www.sntss-xxv.com

sudo certbot certificates

sudo systemctl status certbot.timer

sudo certbot renew --dry-run    

server {
    listen 80;
    server_name sntss-xxv.com www.sntss-xxv.com;
    client_max_body_size 10M;

    proxy_read_timeout 720s;
    proxy_connect_timeout 720s;
    proxy_send_timeout 720s;

    location / {
        proxy_pass http://127.0.0.1:8018;

        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;

        proxy_redirect off;
    }

    location /websocket {
        proxy_pass http://127.0.0.1:8018;

        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";

        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;

        proxy_read_timeout 720s;
    }
}
