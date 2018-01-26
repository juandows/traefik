# traefik
Imagen docker para crear un contenedor traefik con https para docker SWARM

Bajar el repositorio a una carpeta en nuestro equipo local.

Nos conectamos al servidor docker del manager con eval (eval "$(docker-machine env coreOs2)")

Creamos la imagen docker con nuestros parámetros:

$ docker build -t 127.0.0.1/traefik:latest .

Nos conectamos por ssh al manager (  docker-machine ssh coreOs2 ) y subimos a nuestro registry dle SWARM la imagen:

$ docker push 127.0.0.1/traefik:latest

Creamos el servicio traefik:

> docker service create \
--name traefik \
--constraint 'node.role==manager' \
--publish 80:80 \
--publish 8090:8080 \
--mount type=bind,source=/var/run/docker.sock,target=/var/run/docker.sock \
--network core-infra \
traefik:camembert \
--docker \
--docker.swarmmode \
--docker.domain=traefik.redyser.com \
--docker.watch \
--logLevel=DEBUG \
--web
