# Docker

O primeiro passo da configuração do SisGEO é a instalação e configuração do Docker.

## Instalar Docker

Basta seguir a documentação oficial.

## Inicializar swarm

```sh
docker swarm init 
```

## Criar uma rede do tipo <b>overlay</b>

```sh
docker network create --driver overlay --subnet 10.0.0.0/16 --gateway 10.0.0.1 proxy
```