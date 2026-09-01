# container_odoo_16

This repository contains the template to generate the containers

------------------

# GITHUB CONFIGURATION 


eval "$(ssh-agent -s)"; ssh-add ~/.ssh/e.vizcaino@solesgps.com
git remote set-url origin git@github.com:evigra/container_odoo_16.git

git clone --recurse-submodules https://github.com/evigra/container_odoo_16.git

git submodule add git@github.com:evigra/instance_odoo.git addons/instance_odoo


# DOCKER CONTAINER CONFIGURATIONS
sudo chown $(whoami):$(whoami) /var/run/docker.sock


# Modules TEST
docker exec -it container_odoo_16 bash -c "odoo -d container_odoo_16_test -i instance -p 8006--db_host=container_postgres_16 --db_port=5432 --db_user=odoo --db_password=odoo  --without-demo=all"


# show container in console
docker exec -it container_odoo_16 /bin/bash


http://container_odoo_16.localhost:8016
http://container_odoo_16_test.localhost:8006

docker stop container_odoo db16
docker rm container_odoo db16 
clear

