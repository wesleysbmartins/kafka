# Kafka
Apache Kafka é uma plataforma de streaming de eventos distribuída e altamente escalável, projetada para processar e gerenciar grandes volumes de dados em tempo real. Originalmente desenvolvido pela LinkedIn e posteriormente aberto como um projeto de código aberto pela Apache Software Foundation, o Kafka é amplamente utilizado em diversas indústrias para várias aplicações, incluindo:

* **Log de eventos:** Registro de eventos e atividades em sistemas distribuídos.
* **Ingestão de dados:** Coleta e ingestão de grandes volumes de dados de diversas fontes.
* **Streaming de dados em tempo real:** Processamento contínuo de fluxos de dados em tempo real.
* **Pipeline de dados:** Integração de dados entre diferentes sistemas.

## Componentes Principais do Kafka

* **Produtores (Producers):** Entidades que publicam dados (mensagens) em tópicos do Kafka.
* **Consumidores (Consumers):** Entidades que lêem e processam dados de tópicos do Kafka.
* **Tópicos (Topics):** Categorias ou canais onde os dados são publicados e armazenados. Cada tópico é particionado e replicado para garantir escalabilidade e tolerância a falhas.
* **Brokers:** Servidores Kafka que armazenam e gerenciam os dados publicados nos tópicos.

## Funcionamento do Kafka
* **Publicação e Assinatura de Mensagens:** Os produtores enviam mensagens aos tópicos, e os consumidores se inscrevem nesses tópicos para receber as mensagens.
* **Particionamento:** Cada tópico é dividido em várias partições, permitindo o processamento paralelo e distribuído.
* **Replicação:** As partições são replicadas em múltiplos brokers para garantir a alta disponibilidade e tolerância a falhas.
* **Retenção de Dados:** As mensagens podem ser armazenadas por um período configurável, permitindo que os consumidores leiam as mensagens em seu próprio ritmo.

## Apache ZooKeeper
Apache ZooKeeper é um serviço de coordenação centralizado para aplicativos distribuídos. Ele fornece uma maneira confiável de gerenciar a configuração e a sincronização de dados entre nós distribuídos. No contexto do Kafka, o ZooKeeper é utilizado para:

* **Gerenciamento de Metadados:** Armazenar e gerenciar metadados do cluster Kafka, como informações de partições e líderes de partições.
* **Coordenação de Líderes:** Eleição de líderes de partições e gerenciamento de falhas de brokers.
* **Configuração Dinâmica:** Alterações de configuração do Kafka podem ser propagadas dinamicamente sem a necessidade de reiniciar os brokers.

## Preparando Ambiente Kafka
Para instalar e utilizar o Kafka você pode usar como um serviço na sua máquina local, seguindo as instruções do site oficial, porém, eu usarei outra abordagem, utilizando o Kafka com o Docker.

<details>
    <summary>Pré-requisitos</summary>
    
- [Docker](https://docs.docker.com/engine/install/)

- [Docker Compose](https://docs.docker.com/compose/install/#install-compose)

- [Git](https://git-scm.com/downloads)
</details>

Para iniciar o Kafka utilizaremos um arquivo Docker Compose criado e mantido pela Confluent, disponível no [GitHub](https://github.com/confluentinc/cp-docker-images).

Clone o repositório:
```json
git clone git@github.com:confluentinc/cp-docker-images.git
```
Após clonar, navegue até a pasta ***cp-docker-images/examples/kafka-single-node***. Esta pasta deve ter um arquivo ***docker-compose.yml*** semelhante a este:
```yml
---
version: '2'
services:
  zookeeper:
    image: confluentinc/cp-zookeeper:latest
    environment:
      ZOOKEEPER_CLIENT_PORT: 2181
      ZOOKEEPER_TICK_TIME: 2000

  kafka:
    # "`-._,-'"`-._,-'"`-._,-'"`-._,-'"`-._,-'"`-._,-'"`-._,-'"`-._,-'"`-._,-
    # An important note about accessing Kafka from clients on other machines: 
    # -----------------------------------------------------------------------
    #
    # The config used here exposes port 9092 for _external_ connections to the broker
    # i.e. those from _outside_ the docker network. This could be from the host machine
    # running docker, or maybe further afield if you've got a more complicated setup. 
    # If the latter is true, you will need to change the value 'localhost' in 
    # KAFKA_ADVERTISED_LISTENERS to one that is resolvable to the docker host from those 
    # remote clients
    #
    # For connections _internal_ to the docker network, such as from other services
    # and components, use kafka:29092.
    #
    # See https://rmoff.net/2018/08/02/kafka-listeners-explained/ for details
    # "`-._,-'"`-._,-'"`-._,-'"`-._,-'"`-._,-'"`-._,-'"`-._,-'"`-._,-'"`-._,-
    #
    image: confluentinc/cp-kafka:latest
    depends_on:
      - zookeeper
    ports:
      - 9092:9092
    environment:
      KAFKA_BROKER_ID: 1
      KAFKA_ZOOKEEPER_CONNECT: zookeeper:2181
      KAFKA_ADVERTISED_LISTENERS: PLAINTEXT://kafka:29092,PLAINTEXT_HOST://localhost:9092
      KAFKA_LISTENER_SECURITY_PROTOCOL_MAP: PLAINTEXT:PLAINTEXT,PLAINTEXT_HOST:PLAINTEXT
      KAFKA_INTER_BROKER_LISTENER_NAME: PLAINTEXT
      KAFKA_OFFSETS_TOPIC_REPLICATION_FACTOR: 1
```

Abra seu terminal dentro da pasta ***cp-docker-images/examples/kafka-single-node*** e execute o arquivo ***yaml***:
```shell
docker-compose up -d
```

Visualize os conteiners de zookeeper e kafka disponíveis executando:
```shell
docker-compose ps
```

O resultado dese ser semelhante:
```shell
NAME                            IMAGE                              COMMAND                  SERVICE     CREATED              STATUS              PORTS
kafka-single-node-kafka-1       confluentinc/cp-kafka:latest       "/etc/confluent/dock…"   kafka       About a minute ago   Up About a minute   0.0.0.0:9092->9092/tcp
kafka-single-node-zookeeper-1   confluentinc/cp-zookeeper:latest   "/etc/confluent/dock…"   zookeeper   2 minutes ago        Up About a minute   2181/tcp, 2888/tcp, 3888/tcp
```

Finalmente você tem um Kafka disponível para integrar as suas aplicações!
