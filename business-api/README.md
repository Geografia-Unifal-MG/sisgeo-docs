# Business API

API REST que faz o CRUD dos dados armazenados em um banco mongodb. Esses dados são usados para configurar como as layers e visions (seção do sidebar) serão exibidas no webmap.

A stack da Business API também realiza o deploy do mongodb, a tecnologia de banco de dados usada nessa API.

## Configuração do mongodb

Criar algum diretório para que os dados do mongo persistam entre o sistema de arquivos do container e a máquina. Ex:

```s
mkdir /data/mongodb
```

> Esse diretório criado deve ser mapeado em <b>volumes</b> no business-api.yaml para o diretório ```/data/db``` do container do mongo.

## Fazer deploy da stack

```sh
docker stack deploy -c business-api.yaml nomeDaStack
```

## Importação de dados

Hoje, a Business API não tem endpoints para criação de todos os dados que ela disponibiliza. Por isso, será necessário importar os dados diretamente no mongodb. Para importar esses dados siga as instruções do script [mongodb-import-export](https://github.com/Geografia-Unifal-MG/sisgeo-scripts/tree/main/business-api/mongodb-import-export).

## Autenticação do mongodb

As credenciais do mongodb devem ser definidas no arquivo ```business-api.jar/BOOT-INF/classes/application-docker.properties```, que se encontra do diretório de arquivos do container da Business API.

É necessário criar o usuário <b>business-api</b> no banco <b>businessapi</b> do mongodb. Para isso siga os passos:

- Execute shell do mongodb:

```sh
mongo
```

- No shell do mongodb execute
```sh
db = new Mongo("url_mongodb:porta_mongodb").getDB("businessapi");
db.createUser({
	user : "business-api",
	pwd: passwordPrompt(),
	customData : {
		
	},
	roles : [
		{
			"role" : "dbAdmin",
			"db" : "businessapi"
		}
	],
	mechanisms : [
		"SCRAM-SHA-1",
		"SCRAM-SHA-256"
	]
})
```

- Insira a senha desejada

## Dados do negócio 

A seguir, um explicação detalhada do significado de cada collection do banco de dados.

### <b> datasource </b>

Como o próprio nome já diz, define uma fonte de dados, no caso, define a url base para uma layer, podendo ser url base de uma mapa como google streets, google satellite, open street map ou um workspace no geoserver, como prodes amazonia, prodes cerrado.

### <b> layer </b>

Contém as layers do geoserver, com título, descrição, entre outros. Também tem uma referência ao workspace em que ela pertence.

### <b> download </b>

Contém o link para download de shapefiles.

### <b> subdomain </b>

Subdomínio. 

### <b> tool </b>

Contém definições das ferramente que o sistema pode ter como: ferramente para ver a legenda da layer, ferramente que permite remoção da layer, ferramente que permite ver os dados do dashboard.

### <b> visions </b>

Cada linha dessa collection é um menu no sitebar do site, cada vision pode ser abilitada ou desabilitada. Cada vision contém a um array de ids de layers. Também contém a ordem em que as visions vão ser exibidas no sitebar.

### <b> vision to vision </b>

Relaciona uma vision a um array de visions. Por exemplo, tudo que aparece no sitebar é uma vision, logo essa vision(root) vai referenciar todas as outras visions que aparecerâo no sidebar.

## Inserindo dados no mongodb

### <b> Workspace </b>

Para inserir um <b>workspace</b>, insira um registro na collection <b>datasource </b>. Na coluna <b>host</b> a uri deve estar no seguinte formato:

```http://url_geoserver:porta_geoserver/geoserver/nome_do_workspace/wms```
