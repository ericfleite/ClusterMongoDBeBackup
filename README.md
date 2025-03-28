# Criação de um cluster com o Docker e o MongoDB e como fazer o Backup e restore de um banco de dados no MongoDB
![MongoDB](https://img.shields.io/badge/MongoDB-%234ea94b.svg?style=for-the-badge&logo=mongodb&logoColor=white) ![Docker](https://img.shields.io/badge/docker-%230db7ed.svg?style=for-the-badge&logo=docker&logoColor=white)

## Menu

<ul>
  <li><a href="#network">Cluster Replica Set</a></li>
  <ul>
    <li><a href="#nos">Criação dos nós</a></li>
    <li><a href="#replica">Iniciando o Replica Set no Docker</a></li>
    <li><a href="#inserir">Inserindo dados no banco de dados no MongoDB Compass</a></li>
    <li><a href="#nosecundario">Derrubando um nó secundário</a></li>
  </ul>
  <li><a href="#">Backup e Restore</a></li>
</ul>

<p id="network"></p>

### 💻  Criação do docker network:

<p>
  Para criar o network do cluster você precisará abrir o terminal do docker e inserir o seguinte prompt (mongoCluster é o nome do network):
</p>

```shell
docker network create mongoCluster
```
<p id="nos"></p>

### ⚙️ Criação dos nós:

<p>
  Para criar os nós, ainda no terminal do doker, é necessário inserir o prompt a seguir:
</p>

<p>
  Primeiro nó:
</p>

```shell
docker run -d --rm -p 27017:27017 --name mongo10 --network mongoCluster mongodb/mongodb-community-server:latest --replSet myReplicaSet --bind_ip localhost,mongo10
```
<p>
  27017 é o endereço, mongo10 é o nome do nó (conteiner), mongoCluster é o network sendo utilizado, mongodb/mongodb-community-server:latest é a imagem sendo utilizada, o nome do replica set é myReplicaSet, localhost é onde será hospedado.
</p>

<p>
  Segundo nó:
</p>

```shell
docker run -d --rm -p 27018:27017 --name mongo20 --network mongoCluster mongodb/mongodb-community-server:latest --replSet myReplicaSet --bind_ip localhost,mongo20
```

<p>
  27018 é o endereço, mongo10 é o nome do nó (conteiner), mongoCluster é o network sendo utilizado, mongodb/mongodb-community-server:latest é a imagem sendo utilizada, o nome do replica set é myReplicaSet, localhost é onde será hospedado.
</p>

<p>
  Terceiro nó:
</p>

```shell
docker run -d --rm -p 27019:27017 --name mongo30 --network mongoCluster mongodb/mongodb-community-server:latest --replSet myReplicaSet --bind_ip localhost,mongo30
```

<p>
  27019 é o endereço, mongo10 é o nome do nó (conteiner), mongoCluster é o network sendo utilizado, mongodb/mongodb-community-server:latest é a imagem sendo utilizada, o nome do replica set é myReplicaSet, localhost é onde será hospedado.
</p>

<p>
  Quarto nó:
</p>

```shell
docker run -d --rm -p 27020:27017 --name mongo40 --network mongoCluster mongodb/mongodb-community-server:latest --replSet myReplicaSet --bind_ip localhost,mongo40
```

<p>
  27020 é o endereço, mongo10 é o nome do nó (conteiner), mongoCluster é o network sendo utilizado, mongodb/mongodb-community-server:latest é a imagem sendo utilizada, o nome do replica set é myReplicaSet, localhost é onde será hospedado.
</p>

<p id="replica"></p>

### 🚀 Iniciando o Replica Set no Docker

<p>
  Utilizando o mongosh no docker, ainda no terminal do docker, utilize o prompt:
</p>

```shell
docker exec -it mongo10 mongosh
```

<p>
  docker exec: Esse é o comando principal para executar um processo dentro de um container que já está em execução. O mongo10: Esse é o nome do container no qual você deseja executar o processo. No seu caso, é o container chamado mongo10. O mongosh: É o comando que será executado dentro do container. mongosh é a nova versão do shell interativo do MongoDB, onde você pode rodar comandos MongoDB diretamente. o -it: Esse comando efetivamente abre uma sessão interativa do MongoDB Shell.
</p>

<p>
  Salve o endereço que aparecerá no campo Connection To, por exemplo:
</p>

```
mongodb://127.0.0.1:27017/?directConnection=true&serverSelectionTimeoutMS=2000&appName=mongosh+2.4.2
```

<p>
  Verifique se o conteiner está executando com o seguinte prompt:
</p>

```shell
db.runCommand ({hello:1})
```

<p>
  Ainda no docker, faça a configuração do replica set dos nós e os ative, inserindo o id de cada um e qual o id do replica set, mostrando qual o membro também, usando o prompt:
</p>

```shell
rs.initiate ({ _id: "myReplicaSet", members:[{_id:0, host: "mongo10"}, {_id:1, host: "mongo20"}, {_id:3, host: "mongo30"}, {_id:4, host: "mongo40"}]})
```

<p>Saia do MongoDB Shell:</p>

```shell
exit
```

<p>
  Verifique o status do cluster e verifique quem é o nó primário:
</p>

```shell
docker exec -it mongo10 mongosh --eval "rs.status()"
```

<p>
  Você pode usar esse comando em outro nó que foi configurado também, como por exemplo o mongo20. E verifique quem é o nó primário.
</p>

<p id="inserir"></p>

### ➕ Inserindo dados no banco de dados no MongoDB

<p>
  Abra o MongoDB Compass, e adicione uma nova conexão, utilize o endereço que foi salvo, e preste atenção em quem é o nó primario, exemplo do nó primário como mongo10 (se fosse o mongo20, você troca o 27017 por 27018, como foi programado anteriormente):
</p>

```
mongodb://127.0.0.1:27017/?directConnection=true&serverSelectionTimeoutMS=2000&appName=mongosh+2.4.2
```

Abra o terminal e verifique se o seu terminal está da seguinte forma:

```
myReplicaSet [direct: primary] test>
```

<p>
  Verifique qual o nó que você está conectado, inserindo o seguinte prompt:
</p>

```shell
rs.isMaster().primary
```

<p>Criação do banco de dados e inserção dos dados</p>

```shell
use ClusterMongo

db.cliente.insertOne({codigo:1, nome: "Marcos"});

db.cliente.insertOne({codigo:2, nome: "Gabriel"});

db.cliente.insertOne({codigo:3, nome: "Eric"});

db.cliente.insertOne({codigo:4, nome: "Ana"});

db.cliente.insertOne({codigo:5, nome: "Juliana"});

```

<p>
  Verifique se os dados foram inseridos.
</p>

```
db.cliente.find()
```

<p id="nosecundario"></p>

### 💥 Derrubando um nó secundário:

<p>
  Foi escolhido o nó mongo40 para derrubar, no terminal do docker digite: 
</p>

```shell
docker stop mongo40
```

<p>
  Você irá verificar que o conteiner do mongo40 desapareceu. Para confirmar digite no terminal:
</p>

```shell
docker exec -it mongo10 mongosh --eval "rs.status()"
```

<p>
  Você verificará que o nó caiu.
</p>

<p>
  No MongoDB Compass, adicione um dado. E você verificará que o cluster ainda está funcionando normalmente. Exemplo:
</p>

```shell
db.cliente.insertOne({codigo:8, nome: "João"});
```

<p>
  Para restabelecer o nó, utilize o seguinte prompt (lembrando que foi o mongo40 que foi derrubado):
</p>

```shell
docker run -d --rm -p 27020:27017 --name mongo40 --network mongoCluster mongodb/mongodb-community-server:latest --replSet myReplicaSet --bind_ip localhost,mongo40
```

<p>
  Verifique o status do nó com o prompt abaixo:
</p>

```shell
docker exec -it mongo10 mongosh --eval "rs.status()"
```

<p>
  Você verificará que o nó voltou e está ativo como secundário.
</p>

