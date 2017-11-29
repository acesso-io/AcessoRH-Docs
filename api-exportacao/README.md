# **API**

 Plataforma da Acesso RH para exportação de relatório em layout customizado.

## Índice

##### 1. [**Ativação da API**](#ativação-da-api)
##### 2. [**Consultando uma conta**](#consultando-uma-conta)
##### 3. [**Consultando múltiplas contas**](#consultando-múltiplas-contas)
##### 4. [**Filtros**](#filtros)
##### 5. [**Glossário**](#glossário)

### Ativação da API

Para a utilização da API é nescessária uma conta de serviço. Esta conta pode ser criada através de um procedimento descrito no tutorial a seguir:

* Uma credencial de conta de serviço inclui um nome único de conta, um identificador da empresa (Tenant ID) e ao menos um par de chave pública/privada.

* Para gerar uma credencial de conta de serviço é preciso nos enviar os seguintes dados:
   - Nome da empresa
   - CNPJ da empresa
   - Nome da aplicação
   - Nome e email do responsável pela
     aplicação na empresa
   - Email do responsável pela
     aplicação na empresa

* Após o recebimento desses dados, será criada uma conta de serviço responsável por autenticar a sua aplicação. Enviaremos o nome desta conta em conjunto com o identificar da empresa (Tenant ID).

* Para que a utilização da conta de serviço seja possível, é preciso que você crie o par de chave pública/privada e nos envie apenas a chave pública para associarmos ela à conta de serviço. 

  Uma forma eficaz de criar a chave pública/privada é utilizando a ferramenta OpenSSL. Para tal, é preciso criar um arquivo com as informações da requisição da criação de certificados. Salvar no arquivo openssl_idp.cnf os dados abaixo:

```
[ req ]
default_bits = 2048
default_keyfile = privkey.pem
string_mask = utf8only
distinguished_name = req_distinguished_name
req_extensions = v3_req
x509_extensions = v3_ca
[ req_distinguished_name ]
commonName = Common Name (e.g. server FQDN or YOUR name)
commonName_max = 64
SET-ex3 = SET extension number 3
[ v3_req ]
basicConstraints = critical, CA:FALSE
keyUsage = critical, nonRepudiation, digitalSignature
extendedKeyUsage = critical, clientAuth
[ v3_ca ]
basicConstraints = critical, CA:FALSE
keyUsage = critical, nonRepudiation, digitalSignature
extendedKeyUsage = critical, clientAuth
```

  Uma vez criado o arquivo openssl_idp.cnf basta executar o comando a seguir no console, substituindo os parâmetros de acordo com as instruções da tabela abaixo, para que o OpenSSL crie a chave pública/privada:

| Parâmetro        | Valor           |
| ------------- |:-------------:|
| -days         | Tempo de validade do certificado. Por padrão 730 dias (2 anos). |
| -subj         | O valor deverá utilizar o formato “/CN=service_account_name@tenant_id.iam.acesso.io”, onde service_account_name deverá ser o nome da conta de serviço recebida por nós e o tenant_id deverá ser o identificador da empresa também recebido por nós (no formato UUID versão 4). Por exemplo, para a conta de serviço de nome svcapp1da empresa 9ea3c3bd-4447-4c3b-ae2e-504b795d3733 (tenant_id), o valor deverá ser “/CN=svcapp1@9ea3c3bd-4447-4c3b-ae2e-504b795d3733.iam.acesso.io”.|
| -keyout       | O valor aqui, utilizando o exemplo anterior, deverá ser svcapp1.key.pem no lugar de service_account_name.key.pem.|
| -out          | O valor aqui, utilizando o exemplo anterior, deverá ser svcapp1.cert.pem no lugar de service_account_name.cert.pem.|



```
openssl req -x509 -new -nodes -sha1 -days 730 -newkey rsa:2048 \
-subj "/CN=service_account_name@tenant_id.iam.acesso.io" \
-keyout service_account_name.key.pem -out service_account_name.cert.pem \
-config ./openssl_idp.cnf
```

  Concluída a criação da chave pública/privada, você deverá nos enviar apenas a chave pública, no exemplo, o arquivo svcapp1.cert.pem para que possamos armazená-la na plataforma de autenticação associada à sua conta de serviço. A plataforma de autenticação suporta até o momento apenas chaves no formato PEM. Assim que a chave pública estiver associada à sua conta de serviço na plataforma de autenticação já será possível fazer requisições de autenticação da conta de serviço.

* Fazendo requisições de autenticação da conta de serviço:

Após a criação e configuração de uma conta de serviço, sua aplicação precisa completar as
seguintes etapas:
###### 1. Criar um JSON Web Token (JWT), que inclui cabeçalho, payload e assinatura;
###### 2. Requisitar um token de acesso (AccessToken) da plataforma de autenticação OAuth2;
###### 3. Tratar a resposta JSON que a plataforma de autenticação retornará.Se na resposta estiver incluso um token de acesso, você poderá usar o token de acesso para fazer requisições às APIs dos produtos ACESSO para os quais a conta de serviço possui permissão de acesso. (Se na resposta não estiver incluso um token de acesso, seu JWT e requisição de obtenção do token podem estar incorretos ou a conta de serviço pode não ter as permissões necessárias para acessar os recursos solicitados.) Quando o token de acesso expira, sua aplicação cria um novo JWT, faz a assinatura e requisita um novo token de acesso.

O Diagrama a seguir ilustra a sequência para a autenticação:

![Diagrama](https://github.com/acesso-io/AcessoRH-Docs/tree/master/assets/images/diagram.png)

Exemplo do Payload Antes da codificação: 

```
{"alg":"RS256","typ":"JWT"}.
{
"iss":"service_account_name@tenant_id.iam.acesso.io",
"aud":"https://identity.acesso.io",
"scope":"*",
"exp":1328554385,
"iat":1328550785
}.
[byte array da assinatura]
```


Token portador JWT:

```
eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJzZXJ2aWNlX2FjY291bnRfbmFtZUB0ZW5hbnRfaWQuaWFtLmFjZXNzby5pbyIsInN1YiI6InVzZXJfaWRlbnRpZmllciIsImF1ZCI6Imh0dHBzOi8vaWRlbnRpdHkuYWNlc3NvLmlvL29hdXRoMi90b2tlbiIsInNjb3BlIjoiKiIsImV4cCI6MTMyODU1NDM4NSwiaWF0IjoxMzI4NTUwNzg1fQ.R5PT3GRtsg5UJYr-ZOquzbnnexUO4JLKNvOOhHEZn0UiR1ugSMxeBBgfq52Iu_JJB5NwHnfHyuhRLpglOdePM4jlrKE_k7uvhOKUYccg6Su-lnBG-L8yekz7C076iatmJAdmBthuB9qjZzqk0EKSC0JmiykzH9xnRqEJSi0aTPM
```

Este é o token usado para fazer as chamadas REST durante seu tempo de validade.

Para autenticar-se, o cliente deverá enviar uma requisição POST https://identity.acesso.io/oauth2/token  com um corpo x-wwww-form-urlencoded com os seguintes parâmetros:

```
grant_type:urn:ietf:params:oauth:grant-type:jwt-bearer
assertion: colocar o token de serviço JSW aqui
scope:service:api
```


### Consultando uma conta

Para consultar uma conta é necessário um [token](#token) com permissão de acesso a dados de uma organização, e o UID de uma conta específica.

```
curl -X POST -H "Authorization: Bearer {token}" 'https://api.acessorh.com.br/positions'
```

No corpo da requisição deve ser enviado um objeto em JSON. Um objeto JSON utiliza pares de nome(ou rótulo)/valor onde um par deve ser representado pelo nome entre aspas duplas, seguido de dois pontos, seguido do valor.

O rótulo "accounts" aceita um array de strings contendo os UID's das contas. Logo, basta realizar a requisição com o UID da conta que deseja obter os dados.

Exemplo do corpo da requisição em [JSON](#json):

```
{
  "accounts": ["XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX"],
  "limit": 100,
  "skip": 0,
  "template":"csv-v2.0",
  "sort":"-created_at",
  "status": ["archived"]
}

```



### Consultando múltiplas contas

Para realizar a consulta em mais de uma conta é necessário um [token](#token) com permissão de acesso a dados de uma organização, e o UID de cada uma das contas. O corpo da requisição não se altera, basta apenas passar no corpo todos os UID's de contas desejados:

```
curl -X POST -H "Authorization: Bearer {token}" 'https://api.acessorh.com.br/positions'
```


No corpo da requisição deve ser enviado um objeto em JSON. Um objeto JSON utiliza pares de nome(ou rótulo)/valor onde um par deve ser representado pelo nome entre aspas duplas, seguido de dois pontos, seguido do valor.

O rótulo "accounts" aceita um array de strings contendo os UID's das contas. Logo, basta realizar a requisição com o UID da conta que deseja obter os dados.

Exemplo do corpo da requisição em [JSON](#json):

```
{
  "accounts": ["XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX", "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX", "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX"],
  "limit": 100,
  "skip": 0,
  "template":"csv-v2.0",
  "sort":"-created_at",
  "status": ["archived"]
}

```

### Filtros

Há ainda opções de configurar algumas opções de filtros utilizando outros rótulos do objeto. São eles:

* Limite de registros
  
  A rótulo "limit" fornece opção de configurar um limite máximo de registros que devem vir no response. Porém, deve-se estar atento ao fato de que existe um limite máximo de **100** registros por requisição. Caso sejá nescessário consultar um número maior, deve-se utilizar o parâmero de "skip" comentado a seguir e realizar mais de uma consulta.

  Obs. Por padrão, se o limite não for informado no corpo da requisição será utilizado um valor padrão de **50** registros. 


* Skip
  
  Caso seja nescessário realizar alguma paginação ou simplesmente recuperar um número superior ao limite de registros por requisição a aplicação deverá utilizar a rótulo "skip". Este permite que um dado número de registros seja desconsiderado na consulta.


* Modelo de exportação
  
  Pode-se escolher qual o modelo a ser utilizado utilizando o rótulo "template". Abaixo estão descritas as opções existentes:

  [csv-v2.0](https://github.com/acesso-io/AcessoRH-Docs/tree/master/assets/templates/csv-v2.0.csv) - Padrão da Acesso de csv com todos os dados dos colaboradores.


* Ordenação
  
  O rótulo "sort" possibilita a ordenação dos dados. Por padrão é utilizado a data de criação da vaga ordenado do mais angita até a adicionada mais recentemente, porém pode ser alterado utilizando algum dos valores a seguir:

  "created" - Ordena os registros de colaboradores recuperados por data de criação da vaga a partir dos mais antigos

  "-created" - Ordena os registros de colaboradores recuperados por data de criação da vaga a partir dos mais recentes

  "updated" - Ordena os registros de colaboradores recuperados por data de atualização(ultima vez que mudou de status) a partir dos mais antigos

  "-updated" - Ordena os registros de colaboradores recuperados por data de atualização(ultima vez que mudou de status) a partir dos mais recentes


* Status do colaborador

  É possivel definir quais colaboradores exportar pelos status em que se encontram. Assim, um array especificado pelo rótulo "status" deve ser utilizado e caso não seja especificado a API irá retornar por padrão os registros com status em arquivado.  

  As opções disponíveis de filtro para status são:

  "invited" - Colaboradores que foram cadastrados, porém não acessaram a plataforma e realizaram login.
     
  "pending" - Colaboradores que já acessaram porém não concluiram o cadastro dos documentos.
      
  "review" - Registros que tiveram alguma documentação negada e estão realizando a revisão dos mesmos.
    
  "validation" - Colaboradores que concluiram o cadastro dos documentos e estão sendo validados pelo sistema.
    
  "completed" - Colaboradores que foram conluídos.
      
  "archived" - Colaboradores que foram arquivados.

Exemplo:
```
{
  "accounts": ["XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX", "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX", "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX"],
  "limit": 100,
  "skip": 0,
  "template":"csv-v2.0",
  "sort":"-created_at",
  "status": ["pending","archived"]
}

```

### Glossário

<a id="chamada_rest">Chamada REST</a>
: Na chamada REST as informações são acessadas através de endpoints em formato HTTPS, utilizando os seguintes comandos para realizar as ações:

|Comandos|Descrição|
|--------|-----|
|GET     |Usado para retornar informação.
|POST    |Usado para criar informação.
|PUT     |Usado para alterar/repor informação.
|DELETE  |Usado para deletar informação.


<a id="json">JSON</a>
: JSON é uma formatação leve de troca de dados. Para seres humanos, é fácil de ler e escrever. Para máquinas, é fácil de interpretar e gerar. Está baseado em um subconjunto da linguagem de programação JavaScript.

<a id="timestamps">Timestamps</a>
: O formato de data e hora que utilizamos é o padrão [RFC3339](https://www.ietf.org/rfc/rfc3339.txt) com fuso horário em UTC. Ex.:

```json
"2005-10-30 10:45:00.000"
```

<a id="token">Token</a>
: O Token é uma chave de acesso gerada apartir de um Refresh Token. Através do Token é possivel acessar a API e realizar [chamadas REST](#chamada_rest).

<a id="refresh_token">Refresh Token</a>
: Refresh Token é um token especial gerado no dashboard da organização, que contem as informações necessárias para gerar um token de acesso.
