# Geoserver

Ferramenta que possibilita a visualização e ediçao de dados geoespaciais. Ele será indispensável no webmap, pois tem funcionalidades WMS para servir mapas, respondendo a requisições do webmap das imagens dos mapas. Aqui também realizaremos o deploy do container nginx, que auxilia no funcionamento desse container do geoserver.

## Criar diretórios

Criar diretório de dados do Geoserver:

```sh
mkdir /data/geoserver
```

Caso utilize a stack v1 (```docker-compose/v1/geoserver.yaml```), também será feito o deploy o nginx (definido no geoserver.yaml). Nesse caso será necessário criar mais alguns diretórios e arquivos:

```sh
mkdir /data/nginx
mkdir /data/nginx/geoserver
cd /data/nginx/geoserver
mkdir conf.d
mkdir log
```

## Adicionar layers ao Geoserver

Existem algumas formas de popular o Geoserver com dados:

- [Pela interface gráfica](#adicionar-layers-manualmente-pela-gui)
- Referenciar um diretório com os arquivos das layer e os arquivos dos metadados das layers (arquivos .xml)

## Alterar arquivo de configuração

O arquivo de stack (geoserver.yaml) referencia um arquivo web.xml. Essa é o arquivo de configuração do Geoserver. Ele está em ```/geoserver/data``` deste repositório. É necessário certificar-se que ele contém algumas configurações:

### Cross origin policy

O arquivo <b>web.xml</b> deve estar definido para permitir a política allow-cross-origin, para que o geoserver aceite requisições de outros domímios (utilizado em GetFeatureInfo: ao clicar com o botão direito do mouse na layer no webmap):

```xml
<filter>
  <filter-name>CorsFilter</filter-name>
  <filter-class>org.apache.catalina.filters.CorsFilter</filter-class>
  <init-param>
    <param-name>cors.allowed.origins</param-name>
    <param-value>*</param-value>
  </init-param>
</filter>

<filter-mapping>
  <filter-name>CorsFilter</filter-name>
  <url-pattern>/*</url-pattern>
</filter-mapping>
```

### Geoserver whitelist

Para poder acessar a todas as funcionalidades da interface do Geoserver, é necessário adicionar o domínio onde ele será servido à <b>GEOSERVER_CSRF_WHITELIST</b>. Ela é feita para evitar o erros como "<i>Origin does not correspond to request</i>" ao fazer algumas operações no site do Geoserver.

Ex: para poder acessar o Geoserver pela url https://meu-site.com.br/geoserver adicione:

```xml
<context-param>
  <param-name>GEOSERVER_CSRF_WHITELIST</param-name>
  <param-value>meu-site.com.br</param-value>
</context-param>
```

## Fazer deploy da stack

```sh
docker stack deploy -c geoserver.yaml nomeDaStack
```

## Adicionar layers manualmente pela GUI

- Acessar o geoserver

- Criar um "workspace". Ele serve para agrupar imagens da mesma categoria. Cada "workspace" terá uma url base.

- Criar uma "store". Ela irá definir o caminho do arquivo .shapefile ou outros formatos.

- Adicionar uma layer.

  - Ao acessar o menu de criar layer, (i) clicar em "bounding boxes" (ii) depois em "Compute from data" (iii) e em seguida em "Compute from native bounds"
  - Na aba "publishing" selecionar "line" em "default style"
  - Clicar em "save"

## Exemplos

URI de exemplo para acessar uma layer do Geoserver:

```
https://meu-site.com.br/geoserver/furnas/wms?
service=WMS
&version=1.1.0
&request=GetMap
&layers=furnas:OLI_08_08_16
&styles=
&bbox=362998.7018559398,7617770.953942206,479548.7018559389,7733990.953942206
&width=768
&height=765
&srs=EPSG:32723
&format=application/openlayers
```
