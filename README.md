# traefik
Imagen docker para crear un contenedor traefik con https para docker SWARM

Bajar el repositorio a una carpeta en nuestro equipo local.

Nos conectamos al servidor docker del manager con eval (eval "$(docker-machine env coreOs2)")

Creamos la imagen docker con nuestros parámetros:

$ docker build -t 127.0.0.1:5000/traefik:latest .

Nos conectamos por ssh al manager (  docker-machine ssh coreOs2 ) y subimos a nuestro registry dle SWARM la imagen:

$ docker push 127.0.0.1:5000/traefik:latest

Creamos el servicio traefik:

> docker service create --name traefik --constraint 'node.role==manager' --publish 80:80 --publish 443:443 --publish 8090:8080  --mount type=bind,source=/var/run/docker.sock,target=/var/run/docker.sock --network core-infra 127.0.0.1:5000/traefik:latest --docker --docker.swarmmode --docker.domain=coretest.redyser.com --docker.watch --logLevel=DEBUG --web

> docker service create --name traefik --constraint 'node.role==manager' --publish 80:80 --publish 443:443 --publish 8090:8080  --mount type=bind,source=/var/run/docker.sock,target=/var/run/docker.sock --network core-infra 127.0.0.1:5000/traefik:latest --docker --docker.swarmmode --docker.domain=coretest.redyser.com --docker.watch --logLevel=DEBUG

Hay que iniciar de nuevo el servicio traefik para que se coja el certificado del archivo acme.json.
La primera vez entrar y coger el certifiado (FROM traefik:1.5-alpine). Luego podemos volver a poner (FROM traefik)
Podemos tener el archivo acme.json en el host de docker. Como sólo tenemos un manager lo pondremos ahí:
> docker-machine ssh coreOs2
> mkdir /etc/traefik
> cd /etc/traefik
> sudo vi acme.json ('i', paste contenido archivo acme.json y ':qw')
> sido chmod 600 acme.json

> docker service create --name traefik --constraint 'node.role==manager' --publish 80:80 --publish 443:443 --publish 8080:8080  --mount type=bind,source=/var/run/docker.sock,target=/var/run/docker.sock  --mount type=bind,source=/etc/traefik/acme.json,target=/acme.json --network core-infra 127.0.0.1:5000/traefik:latest --docker --docker.swarmmode --docker.domain=coretest.redyser.com --docker.watch --logLevel=DEBUG
