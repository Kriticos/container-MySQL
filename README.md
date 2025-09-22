# Projeto MySQL com Docker Compose

Este repositório fornece um ambiente Docker Compose para provisionar um container MySQL com configuração personalizada via variáveis de ambiente.

## Pré-requisitos

- Docker e Docker Compose.
- Rede Docker `network-share` já criada:

```bash
# Criar a rede externa se ainda não existir
docker network create --driver bridge network-share --subnet=172.18.0.0/16
```

### OBSERVAÇÃO

**Ajuste a subnet conforme necessário.**

## Estrutura de arquivos

```plaintext
bskp/
├── databases/               # Diretório principal do container MySQL
└── ctr-mysql/               # Projeto MySQL
     ├── docker-compose.yml  # Definição dos serviços Docker
     ├── .env.example        # Exemplo de variáveis de ambiente
     └── README.md           # Documentação do serviço
```

## Configuração das variáveis de ambiente

Copie o template e preencha os valores:

```bash
cp /bskp/ctr-mysql/.env.example /bskp/ctr-mysql/.env
```

Ajuste as variáveis no arquivo `.env`.

## Subindo o serviço

Para iniciar o container em segundo plano:

```bash
docker compose up -d
```

Verifique se o container está em execução:

```bash
docker ps | grep ctr-mysql
```

Acompanhe os logs:

```bash
docker logs -f ctr-mysql
```

## Restartando base de dados

Copia o backup do banco para a pasta tmp do servidor docker

No servidor docker:

### Copiar o backup da base de dados para o container

```bash
docker cp /tmp/<NOME_DO_BACKUP.sql> ctr-mysql:/tmp/<NOME_DO_BACKUP.sql>
```

### Restaurar o backup dentro do container

Acesse o container

```bash
docker exec -it ctr-mysql /bash
```

Verifique se o banco de dados existe


```bash
mysql -u root -p'SENHA_DO_ROOT'

SHOW DATABASES;
```

Se não existir, crie o banco de dados

```bash
mysql -u root -p'SENHA_DO_ROOT'

CREATE DATABASE nome_do_banco;
```

Restaure o backup

```bash
mysql -u root -p'SENHA_DO_ROOT' nome_da_base_de_dados < /tmp/NOME_DO_BACKUP.sql
```
