# API Node.js com Serverless Framework em ambiente AWS

Este repositório contém o código fonte do Live Coding da DIO (Digital Innovation One) realizado em 29/07/2021. O projeto consiste na criação de uma infraestrutura na nuvem AWS usando API Gateway, DynamoDB, AWS Lambda e AWS CloudFormation, utilizando o framework Serverless para o desenvolvimento baseado em Infraestrutura como Código.

## Etapas

### Pré-requisitos:
- Ter uma conta na AWS
- Instalar o Node.js na máquina
- Instalar o AWS CLI: [Documentação do AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-welcome.html)

### Configuração inicial

#### Credenciais AWS
1. Criar um usuário: AWS Management Console -> IAM Dashboard -> Create New User -> <nome do usuário> -> Permissions "Administrator Access" -> Programmatic Access -> Download Keys
2. No terminal, executar o comando: 
   ```
   $ aws configure
   ```
   Colar as credenciais geradas anteriormente quando solicitado.

#### Configuração do framework Serverless
No terminal, executar o seguinte comando para instalar o Serverless globalmente:
```
$ npm i -g serverless
```

### Desenvolvimento do projeto

#### Setup inicial
1. No terminal, executar o seguinte comando:
   ```
   $ serverless
   ```
   Responder "No" para Login/Register e "No" para Update.
   Selecionar "Node.js REST API" como Type.
   Digitar "dio-live" como Name.
2. Navegar para o diretório do projeto:
   ```
   $ cd dio-live
   ```
3. Abrir o projeto no editor de código de sua preferência:
   ```
   $ code .
   ```

#### Configuração do arquivo serverless.yml
No arquivo `serverless.yml`, adicione a região `region: us-east-1` dentro do escopo de `provider`. Salve o arquivo.

#### Deploy da infraestrutura
No terminal, execute o seguinte comando para realizar o deploy:
```
$ serverless deploy -v
```

#### Estruturação do código
1. Crie um diretório chamado "src" e mova o arquivo "handler.js" para dentro dele.
2. Renomeie o arquivo "handler.js" para "hello.js".
3. Atualize o código no arquivo "hello.js" para o seguinte:
   ```javascript
   const hello = async (event) => {
     // código da função hello
   };
   
   module.exports = {
     handler: hello
   };
   ```

#### Atualização do arquivo serverless.yml
Atualize o campo `handler` no arquivo `serverless.yml` para `src/hello.handler`.
No terminal, execute o seguinte comando para realizar o deploy:
```
$ serverless deploy -v
```

#### DynamoDB
1. Atualize o arquivo `serverless.yml` com as seguintes configurações:
   ```yaml
   resources:
     Resources:
       ItemTable:
         Type: AWS::DynamoDB::Table
         Properties:
           TableName: ItemTable
           BillingMode: PAY_PER_REQUEST
           AttributeDefinitions:
             - AttributeName: id
               AttributeType: S
           KeySchema:
             - AttributeName: id
               KeyType: HASH
   ```

#### Desenvolvimento de funções Lambda
1. Crie uma pasta `/src` no repositório.
2. Obtenha o ARN da tabela no Dynamo


DynamoDB
- Obtenha o ARN da tabela no Console da AWS: AWS Console -> DynamoDB -> Visão geral -> Amazon Resource Name (ARN).
- Atualize o arquivo `serverless.yml` com o seguinte código, logo abaixo de `region:`:
  ```yaml
  iam:
    role:
        statements:
          - Effect: Allow
            Action:
              - dynamodb:PutItem
              - dynamodb:UpdateItem
              - dynamodb:GetItem
              - dynamodb:Scan
            Resource:
              - <ARN-da-tabela-no-DynamoDB>
  ```

Instalação de dependências
No terminal, execute os seguintes comandos para inicializar o projeto e instalar as dependências:
```
npm init
npm i uuid aws-sdk
```

Atualização da lista de funções no arquivo `serverless.yml`
Atualize o arquivo `serverless.yml` com a lista de funções a seguir, logo abaixo da seção `functions:`:
```yaml
functions:
  hello:
    handler: src/hello.handler
    events:
      - http:
          path: /
          method: get
  insertItem:
    handler: src/insertItem.handler
    events:
      - http:
          path: /item
          method: post
  fetchItems:
    handler: src/fetchItems.handler
    events:
      - http:
          path: /items
          method: get
  fetchItem:
    handler: src/fetchItem.handler
    events:
      - http:
          path: /items/{id}
          method: get
  updateItem:
    handler: src/updateItem.handler
    events:
      - http:
          path: /items/{id}
          method: put
```

Pronto! Agora você tem um ambiente de API Node.js com Serverless Framework na AWS. Certifique-se de adicionar a lógica e implementar as funções específicas de cada rota de API necessária para o seu projeto.
