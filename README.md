# Criação de um cluster com o Docker e o MongoDB e como fazer o Backup e restore de um banco de dados
![MongoDB](https://img.shields.io/badge/MongoDB-%234ea94b.svg?style=for-the-badge&logo=mongodb&logoColor=white) ![Docker](https://img.shields.io/badge/docker-%230db7ed.svg?style=for-the-badge&logo=docker&logoColor=white)

## Menu

<ul>
  <li><a href="#network">Cluster Replica Set</a></li>
  <ul>
    <li><a href="#nos">Criação dos nós</a></li>
    <li><a href="#replica">Iniciando o Replica Set no Docker</a></li>
  </ul>
  <li><a href="#">Backup e Restore</a></li>
</ul>

<p id="network"></p>

### 💻  Criação do docker network:

<p>
  Para criar o network do cluster você precisará abrir o terminal do docker e inserir o seguinte prompt (mongoCluster é o nome do network):
</p>

```
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

```
docker run -d --rm -p 27017:27017 --name mongo10 --network mongoCluster mongodb/mongodb-community-server:latest --replSet myReplicaSet --bind_ip localhost,mongo10
```
<p>
  27017 é o endereço, mongo10 é o nome do nó (conteiner), mongoCluster é o network sendo utilizado, mongodb/mongodb-community-server:latest é a imagem sendo utilizada, o nome do replica set é myReplicaSet, localhost é onde será hospedado.
</p>

<p>
  Segundo nó:
</p>

```
docker run -d --rm -p 27018:27017 --name mongo20 --network mongoCluster mongodb/mongodb-community-server:latest --replSet myReplicaSet --bind_ip localhost,mongo20
```

<p>
  27018 é o endereço, mongo10 é o nome do nó (conteiner), mongoCluster é o network sendo utilizado, mongodb/mongodb-community-server:latest é a imagem sendo utilizada, o nome do replica set é myReplicaSet, localhost é onde será hospedado.
</p>

<p>
  Terceiro nó:
</p>

```
docker run -d --rm -p 27019:27017 --name mongo30 --network mongoCluster mongodb/mongodb-community-server:latest --replSet myReplicaSet --bind_ip localhost,mongo30
```

<p>
  27019 é o endereço, mongo10 é o nome do nó (conteiner), mongoCluster é o network sendo utilizado, mongodb/mongodb-community-server:latest é a imagem sendo utilizada, o nome do replica set é myReplicaSet, localhost é onde será hospedado.
</p>

<p>
  Quarto nó:
</p>

```
docker run -d --rm -p 27020:27017 --name mongo40 --network mongoCluster mongodb/mongodb-community-server:latest --replSet myReplicaSet --bind_ip localhost,mongo40
```

<p>
  27020 é o endereço, mongo10 é o nome do nó (conteiner), mongoCluster é o network sendo utilizado, mongodb/mongodb-community-server:latest é a imagem sendo utilizada, o nome do replica set é myReplicaSet, localhost é onde será hospedado.
</p>

<p id="replica"></p>

### 🚀 Iniciando o Replica Set no Docker
