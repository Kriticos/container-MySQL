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
|__ mysql-init/           # Diretório do projeto
├── docker-compose.yml    # Definição dos serviços Docker
├── .env.example          # Exemplo de variáveis de ambiente
└── README.md             # Este arquivo de documentação
```

## Configuração das variáveis de ambiente

Copie o template e preencha os valores:

```bash
cp .env.example .env
```

No arquivo `.env`, ajuste:

```bash
# Nome do container
SRV_HOST=<NOME_DO_CONTAINER>
# Imagem MySQL
RELEASE=mysql:8.3

# Credenciais do banco
MYSQL_DATABASE=<NOME_DO_BANCO>
MYSQL_USER=<USUARIO_DO_BANCO>
MYSQL_PASSWORD=<SENHA_DO_USUARIO>
MYSQL_ROOT_PASSWORD=<SENHA_DO_ROOT>

# Volumes
VOL01=<PASTA_DO_BANCO>/databases:/var/lib/mysql
VOL02=./mysql-init:/docker-entrypoint-initdb.d
VOL03=/etc/timezone:/etc/timezone:ro
VOL04=/etc/localtime:/etc/localtime:ro

# Porta de exposição
PORTS=3306:3306

# Endereço IP fixo e sub-rede
IPV4_ADDRESS=<IP_DO_CONTAINER>
SUBNET=<SUBNET_DO_CONTAINER>
```

## Subindo o serviço

Para iniciar o container em segundo plano:

```bash
docker compose up -d
```

Verifique se o container está em execução:

```bash
docker ps | grep ${SRV_HOST}
```

Acompanhe os logs:

```bash
docker logs -f ${SRV_HOST}
```

## Parando e removendo containers

Para parar e remover os recursos criados:

```bash
docker compose down
```

## Explicação do docker-compose.yml

```yaml
services:
  srv-mysql:
    image: ${RELEASE}                 # Imagem MySQL definida no .env
    container_name: ${SRV_HOST}       # Nome do container
    environment:                       # Variáveis para criar o banco e usuários
      - MYSQL_DATABASE=${MYSQL_DATABASE}
      - MYSQL_USER=${MYSQL_USER}
      - MYSQL_PASSWORD=${MYSQL_PASSWORD}
      - MYSQL_ROOT_PASSWORD=${MYSQL_ROOT_PASSWORD}
    command: --character-set-server=utf8 --collation-server=utf8_bin
    volumes:
      - ${VOL01}                      # Persistência dos dados
      - ${VOL02}                      # Inicialização de scripts SQL
      - ${VOL03}                      # Timezone do sistema
      - ${VOL04}                      # Localtime do sistema
    ports:
      - ${PORTS}                     # Mapeamento de portas
    networks:
      network-share:
        ipv4_address: ${IPV4_ADDRESS} # IP fixo na rede externa
    restart: unless-stopped

networks:
  network-share:
    external: true
    ipam:
      config:
        - subnet: ${SUBNET}            # Faixa de IPs da rede externa
```

## Observações

- Ajuste o `subnet` e `ipv4_address` de acordo com sua infraestrutura.  
- Se adicionar scripts SQL em `./mysql-init` eles serão executados na primeira inicialização do container.  
- Utilize `restart: unless-stopped` para garantir que o serviço reinicie automaticamente.

---
