# Guia para Configurar e Rodar o Backstage com Docker

Esse é o passo a passo para construir e rodar o Backstage usando Docker

## Pré-Requisitos

- Node.js (versão 18)
- Docker
- Yarn

## Passo a Passo

### 1. Projeto backstage

1. Instalar o Backstage com o comando `npx`
   ```bash
   npx @backstage/create-app
   ```
   Isso vai criar a estrutura do projeto.
<img width="903" alt="Captura de Tela 2024-11-01 às 11 48 08" src="https://github.com/user-attachments/assets/e93b9567-6b21-4b5f-b526-fc302215b227">
<img width="1062" alt="Captura de Tela 2024-11-01 às 11 47 43" src="https://github.com/user-attachments/assets/65491027-1f43-43bf-a053-20d81df45087">


### 2. Dependências 

1. Entrar na pasta do projeto:
   ```bash
   cd ponderadaa
   ```
2. Instalar as dependências:
   ```bash
   yarn install --immutable
   ```
3. Compilar o backend:
   ```bash
   yarn build:backend --config ../../app-config.yaml --config ../../app-config.production.yaml
   ```


### 3. Imagem Docker

1. Na raiz do projeto, criar o arquivo `Dockerfile` com 

   ```dockerfile
   FROM node:20-bookworm-slim

   # Instala dependências
   ENV PYTHON=/usr/bin/python3
   RUN apt-get update && apt-get install -y python3 g++ build-essential libsqlite3-dev && rm -rf /var/lib/apt/lists/*

   USER node
   WORKDIR /app

   COPY . .

   # Configuração de produção
   ENV NODE_ENV=production
   ENV NODE_OPTIONS="--no-node-snapshot"

   RUN yarn install --immutable
   RUN yarn tsc
   RUN yarn build:backend

   CMD ["node", "packages/backend", "--config", "app-config.yaml", "--config", "app-config.production.yaml"]
   ```

2. Construir a imagem Docker
   ```bash
   docker build -t backstage .
   ```

### 4. Backstage no Docker

1. Executar 
   ```bash
   docker run -it -p 3000:3000 backstage
   ```
2. Ver no terminal se o servidor iniciou corretamente.


3. Abrir o Backstage no navegador em `http://localhost:3000`.


### 5. Acessando o Catálogo de Serviços

1. No navegador, ir até o catálogo de serviços [neste link](http://localhost:3000/catalog?filters%5Bkind%5D=component).
   
<img width="1676" alt="Captura de Tela 2024-11-01 às 12 03 13" src="https://github.com/user-attachments/assets/568c613d-f61c-4e26-a45c-af6c80fc0dae">



## Conclusão

Seguindo esses passos, você poderá rodar o Backstage localmente e explorar suas funcionalidades de catálogo de serviços.
