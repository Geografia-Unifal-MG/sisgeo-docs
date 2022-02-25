# Portainer

Portainer é uma ferramenta que facilita no gerenciamento de outros containers do Docker. Com ele é possível gerenciar containers através da sua interface gráfica, pelo navegador. Ele é muito útil pra fazer deploy de stacks, remover stacks e visualizar os logs. Tudo isso é possível por comandos Docker, por isso ele não é um container indispensável para o projeto. Aqui, utilizamos ele principalmente para visualizar os logs dos containers, pois é bem mais simples fazer isso pela UI.

## Criar diretório de dados

É necessário criar algum diretório para que os dados do portainer persistam entre o sistema de arquivos do container e o host (máquina onde o Docker está rodando). Ex: criar diretório ```/data/portainer```. Esse diretório é especificado no portainer.yaml, caso queira que esses dados fiquem em outro diretório, é necessário alterar esse arquivo, especificando o diretório correto.

```sh
mkdir /data
mkdir /data/portainer
```

## Alterar permissões de alguns diretórios

Modificar permissão do diretório de dados do Portainer:

```sh
chown -R nobody:nogroup /data/portainer
```

Modificar permissão do diretório de volumes do docker:

```sh
chown -R nobody:nogroup /data-docker/volumes
```

## Fazer deploy da stack

```sh
docker stack deploy -c portainer.yaml nomeDaStack
```

Para verificar se o serviço Portainer está rodando execute:

```sh
docker service ls
```

Com o Portainer rodando normalmente, todos os stacks podem ser feitos através do mesmo, fazendo o acesso pelo navegador, na porta indicada no arquivo portainer.yaml.
