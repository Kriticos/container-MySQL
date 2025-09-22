# Projeto MySQL com Docker Compose

Este repositório fornece um ambiente Docker Compose para provisionar um container MySQL com configuração personalizada via variáveis de ambiente.

## Pré-requisitos

- Docker e Docker Compose instalados na máquina.  
- Rede Docker externa chamada `network-share` já criada:

```bash
# Criar a rede externa se ainda não existir
docker network create --driver bridge <NOME_DA_REDE_DOCKER> --subnet=<SUBNET_DO_CONTAINER>
```

- Arquivo `.env` baseado no exemplo fornecido (`.env.example`).

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
cp .env.example .env
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

## Parando e removendo containers

Para parar e remover os recursos criados:

```bash
docker compose down
```

## Observações

- Ajuste o `subnet` e `ipv4_address` de acordo com sua infraestrutura.  

