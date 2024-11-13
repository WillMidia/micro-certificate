# Sistema de Geração de Certificados 🎓

## Visão Geral 🚀
Este projeto é uma aplicação de microserviços para geração automatizada de certificados de conclusão de curso. A API recebe os dados do aluno, processa a solicitação e encaminha para uma fila de processamento. Um worker consome a fila e gera um PDF do certificado, que é armazenado em cache para permitir acessos rápidos e reduzir o tempo de resposta.

![Imgur Image](https://i.imgur.com/VJUKdlD.png)

---

## Estrutura do Projeto 🔧
A aplicação é organizada em serviços independentes que interagem para oferecer uma solução modular e escalável:
- **API**: Recebe solicitações de emissão de certificados e encaminha para o sistema.
- **Worker**: Processa as solicitações e gera os certificados em formato PDF.
- **Banco de Dados**: PostgreSQL para armazenar dados dos alunos e registrar o status de cada certificado.
- **RabbitMQ**: Fila de mensagens para organizar os pedidos de emissão de certificados.
- **Redis**: Cache para armazenar temporariamente os PDFs gerados, facilitando respostas rápidas para consultas frequentes.

---

## Estrutura de Pastas 📂
A organização dos diretórios foi planejada para facilitar a navegação e a manutenção do código:
```
.
├── api                # Serviço principal de API
│   ├── controllers    # Controladores das rotas
│   ├── routes         # Definição das rotas da API
│   ├── services       # Lógica e regras de negócio
│   └── config         # Configurações e variáveis de ambiente
├── worker             # Serviço responsável pela geração dos certificados
│   ├── templates      # Templates HTML para formatação do certificado
│   └── pdfs           # Armazenamento dos PDFs gerados
├── db                 # Scripts de inicialização do banco de dados
├── docker-compose.yml # Configuração para conteinerização com Docker
└── README.md          # Documentação do projeto
```

---

## Configuração do Ambiente 🛠️

### Pré-requisitos
- **Node.js** instalado
- **Docker** instalado

### Etapas de Configuração

1. **Clone este repositório:**
   ```bash
   git clone https://github.com/WillMidia/micro-certificate.git
   cd micro-certificate
   ```

2. **Configuração de variáveis de ambiente:** Crie arquivos `.env` nas pastas `/api` e `/worker` com as seguintes variáveis:
   ```plaintext
   DATABASE_HOST=db
   DATABASE_USER=user
   DATABASE_PASSWORD=pass
   DATABASE_NAME=certificates
   RABBITMQ_HOST=rabbitmq
   REDIS_HOST=redis
   ```
No caso deste projeto, usa-se:
   ```
    DATABASE_HOST=db
    DATABASE_USER=user
    DATABASE_PASSWORD=pass
    DATABASE_NAME=certificates
    RABBITMQ_URL=amqp://rabbitmq:5672
    REDIS_URL=redis://redis:6379

   ```

3. **Inicie a aplicação** com Docker Compose:
   ```bash
   docker-compose up --build
   ```

---

## Como Utilizar a API 🧑‍💻

### Endpoint Principal
- **POST /api/v1/certificate**: Envia os dados do aluno e do curso para emissão de certificado.

### Headers no Postman
Pode-se utilizar Key:`Content-Type` e Value: `application/json`

#### Exemplo do JSON Body:
```json
{
    "nome_aluno": "William Tedros",
    "nacionalidade": "Brasileiro",
    "estado": "SP",
    "data_nascimento": "2003-01-01",
    "data_conclusao": "2024-11-13",
    "rg": "987654321",
    "nome_curso": "Kotlin & Springboot",
    "carga_horaria": 180
}
```
---

## Detalhes dos Serviços 📡

### API Service
- **Responsabilidade**: Recebe requisições para a criação de certificados, valida os dados, e insere na fila RabbitMQ.
- **Localização**: `/api`

### Worker Service 👨‍🏭
- **Responsabilidade**: Consome mensagens da fila, gera o PDF do certificado com base no template HTML e atualiza o status do pedido.
- **Localização**: `/worker`
- **Template de PDF**: O modelo HTML para o certificado está no diretório `/worker/templates`.

### Banco de Dados 🗄️
- **Função**: Gerencia os dados dos alunos e mantém o status de cada certificado.
- **Configuração**: Configurado via Docker Compose com PostgreSQL.

### RabbitMQ 🐰
- **Função**: Orquestra as solicitações de certificados, garantindo processamento assíncrono.
- **Configuração**: Especificado no `docker-compose.yml`.

### Redis ⚡
- **Função**: Cache para os PDFs gerados, melhorando a performance e o tempo de resposta.
- **Configuração**: Incluído no `docker-compose.yml`.

---

## Fluxo de Uso 🚶‍♀️➡️🖨️

1. **Envie uma requisição POST** para `/api/v1/certificate` com os dados completos do aluno.
2. A **API processa e enfileira** o pedido.
3. O **Worker gera o PDF** do certificado e atualiza o banco de dados.
4. O PDF é **armazenado em cache** no Redis para otimizar acessos futuros.

---

## Tecnologias e Ferramentas 🛠️

- **Node.js**: Executa a API e o worker.
- **Express**: Framework de rotas para a API.
- **PostgreSQL**: Banco de dados.
- **RabbitMQ**: Fila de mensagens para controle assíncrono.
- **Redis**: Cache de PDFs gerados.
- **Docker**: Conteinerização e orquestração de serviços.
- **Puppeteer**: Geração de PDF a partir de HTML.

---

## Desenvolvimento Local 💻

1. **Instale as dependências**:
    - Acesse as pastas `/api` e `/worker`:
   ```bash
   npm install
   ```

2. **Inicie a API e o worker** localmente para testes:
   ```bash
   npm start
   ```

3. **Testes e ajustes**: Utilize ferramentas como **Postman** para testar endpoints e verificar a geração dos certificados.
---
## Integrantes

- RM87429 - WILLIAM PEREIRA TEDROS
- RM96106 - JULIA MENEZES DE CARVALHO

