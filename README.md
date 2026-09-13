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


# Modules TEST
docker exec -it container_sindicato_18 bash -c "odoo -d container_sindicato_18_test -i instance_sindicato -p 8006--db_host=container_postgres_16 --db_port=5432 --db_user=odoo --db_password=odoo  --without-demo=all"


# show container in console
docker exec -it container_sindicato_18 /bin/bash


http://container_sindicato_18.localhost:8018
http://container_sindicato_18_test.localhost:8008

docker stop container_odoo db16
docker rm container_odoo db16 
clear

sudo chown evigra:evigra *