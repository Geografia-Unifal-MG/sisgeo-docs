# Webmap

Aplicação web para visualização de imagens geoespaciais.

Requisitos para que o webmap fucione corretamente:

- Geoserver rodando e populado com layers
- Business API rodando e populada com dados de configurações das layers (quais layers serão exibidas no webmap)

## Fazer deploy da stack

```sh
docker stack deploy -c webmap.yaml nomeDaStack
```
