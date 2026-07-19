# CONTAINER

This repository contains the template to generate the containers

------------------

# GITHUB CONFIGURATION 


eval "$(ssh-agent -s)"; ssh-add ~/.ssh/e.vizcaino@solesgps.com
git remote set-url origin git@github.com:evigra/container_solesgps.git

git clone --recurse-submodules https://github.com/evigra/container_solesgps.git

git submodule add git@github.com:evigra/gpsmap.git addons/gpsmap
git submodule add git@github.com:evigra/gpsmap_engine.git addons/gpsmap_engine
git submodule add git@github.com:evigra/gpsmap_history.git addons/gpsmap_history
git submodule add git@github.com:evigra/instance_solesgps.git addons/instance_solesgps




# DOCKER CONTAINER CONFIGURATIONS

sudo chown $(whoami):$(whoami) /var/run/docker.sock


# Create db container 
docker run --restart unless-stopped -d -e POSTGRES_USER=odoo -e POSTGRES_PASSWORD=odoo -e POSTGRES_DB=postgres --name db16 postgres:16

# Create web container 
docker run --restart unless-stopped -v ~/docker/container_solesgps/addons:/mnt/extra-addons -v ~/docker/container_solesgps/config:/etc/odoo -p 8016:8069 -p 8006:8006 --name container_solesgps16 --link db16:db -t odoo:16.0

# Create database
docker exec -ti container_solesgps16 bash -c "/usr/bin/odoo -p 8006  -d www16 --gevent-port=8672 --db_host db --db_port 5432  --db_user odoo --db_password odoo --without-demo all"


# Install modules
docker exec -ti container_solesgps16 bash -c "/usr/bin/odoo -i gpsmap -p 8006 -d www16 --gevent-port=8672 --db_host db --db_port 5432 --db_user odoo --db_password odoo --without-demo all"

docker exec -ti container_solesgps16 bash -c "/usr/bin/odoo -u instance_solesgps,gpsmap,gpsmap_engine -p 8006 -d www165 --gevent-port=8672 --db_host db --db_port 5432 --db_user odoo --db_password odoo --without-demo all"

# show container in console
docker exec -it container_solesgps16 /bin/bash


