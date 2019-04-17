# **API**

 Plataforma da Acesso RH para consulta de cadastros concluídos.

## Índice

1.  [Ativação da API](#ativação-da-api)
    1. [Fazendo requisições de autenticação da conta de serviço](#fazendo-requisições-de-autenticação-da-conta-de-serviço)
2.  [Posições](#posições)
    1. [Criação de novas posições](#criação-de-novas-posições)

### Ativação da API

Para a utilização da API é nescessária uma conta de serviço. Uma credencial de conta de serviço inclui um nome único de conta, um identificador da empresa (Tenant ID) e ao menos um par de chave pública/privada. Para gerar uma credencial de conta de serviço é preciso nos enviar os seguintes dados: 

- Nome da empresa o CNPJ da empresa
- Nome da aplicação
- Nome e email do responsável pela aplicação na empresa
- Email do responsável pela aplicação na empresa

Após o recebimento desses dados, será criada uma conta de serviço responsável por autenticar a sua aplicação. Enviaremos o nome desta conta em conjunto com o identificador da empresa (Tenant ID). 

Para que a utilização da conta de serviço seja possível, é preciso que você crie o par de chave pública/privada e nos envie apenas a chave pública para associarmos ela à conta de serviço. Uma forma eficaz de criar a chave pública/privada é utilizando a ferramenta OpenSSL. Para tal, é preciso criar um arquivo com as informações da requisição da criação de certificados. Salvar no arquivo **openssl_idp.cnf** os dados abaixo: 

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

Uma vez criado o arquivo **openssl_idp.cnf** basta executar o comando a seguir no console, substituindo os parâmetros de acordo com as instruções da tabela abaixo, para que o OpenSSL crie a chave pública/privada:

| Parâmetro | Valor |
| --------- | ----- |
| -days | Tempo de validade do certificado. Por padrão 730 dias (2 anos). |
| -subj | O valor deverá utilizar o formato “/CN=service_account_name@tenant_id.iam.acesso.io”, onde service_account_name deverá ser o nome da conta de serviço recebida por nós e o tenant_id deverá ser o identificador da empresa também recebido por nós (no formato UUID versão 4). Por exemplo, para a conta de serviço de nome svcapp1da empresa 9ea3c3bd-4447-4c3b-ae2e-504b795d3733 (tenant_id), o valor deverá ser “/CN=svcapp1@9ea3c3bd-4447-4c3b-ae2e-504b795d3733.iam.acesso.io”. |
| -keyout | O valor aqui, utilizando o exemplo anterior, deverá ser svcapp1.key.pem no lugar de service_account_name.key.pem. |
| -out | O valor aqui, utilizando o exemplo anterior, deverá ser svcapp1.cert.pem no lugar de service_account_name.cert.pem. |

```
openssl req -x509 -new -nodes -sha1 -days 730 -newkey rsa:2048 \ 
-subj "/CN=service_account_name@tenant_id.iam.acesso.io" \ 
-keyout service_account_name.key.pem -out service_account_name.cert.pem \ 
-config ./openssl_idp.cnf 
```

Concluída a criação da chave pública/privada, você deverá nos enviar apenas a chave pública, no exemplo, o arquivo **svcapp1.cert.pem** para que possamos armazená-la na plataforma de autenticação associada à sua conta de serviço. A plataforma de autenticação suporta até o momento apenas chaves no formato PEM. Assim que a chave pública estiver associada à sua conta de serviço na plataforma de autenticação já será possível fazer requisições de autenticação da conta de serviço. 

### Fazendo requisições de autenticação da conta de serviço

Após a criação e configuração de uma conta de serviço, sua aplicação precisa completar as seguintes etapas: 

1. Criar um JSON Web Token (JWT), que inclui cabeçalho, payload e 
assinatura 
2. Requisitar um token de acesso (AccessToken) da plataforma de 
autenticação OAuth2 
3. Tratar a resposta JSON que a plataforma de autenticação retornará. Se na resposta estiver incluso um token de acesso, você poderá usá-lo para fazer requisições às API’s dos produtos ACESSO para os quais a conta de serviço possui permissão de acesso. Se na resposta não estiver incluso um token de acesso, seu JWT e requisição de obtenção do token podem estar incorretos ou a conta de serviço pode não ter as permissões necessárias para acessar os recursos solicitados. 

Quando o token de acesso expira, sua aplicação cria um novo JWT, faz a assinatura e requisita um novo token de 
acesso. 
O Diagrama a seguir ilustra a sequência para a autenticação: 

![Diagram](/assets/images/diagram.png?raw=true "Diagram")

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

Para autenticar-se, o cliente deverá enviar uma requisição POST https://identity.acesso.io/oauth2/token  com um corpo `x-wwww-form-urlencoded` com os seguintes parâmetros:

```
grant_type:urn:ietf:params:oauth:grant-type:jwt-bearer
assertion: colocar o token de serviço JSW aqui
scope:service:api
```

### Posições
Nosso sistema possibilita que a organização crie novas posições ou consulte as existentes conforme a necessidade. O passo a passo para realizar cada uma dessas operações está descrito em datalhes nos tópicos a seguir.

### Criação de novas posições

Para criar uma nova posição é necessário um token  com permissão de acesso a dados de uma organização, e o UID de uma conta específica.

```
curl --X POST -H "Authorization: Bearer {token}" 'https://api.acessorh.com.br/v1/:unit/json/position'
```

Alterar o parâmetro `:unit` da URL pelo UID da filial onde deseja criar a posição.

No corpo da requisição deve ser enviado um objeto em JSON. Um objeto JSON utiliza pares de nome(ou rótulo)/valor onde um par deve ser representado pelo nome entre aspas duplas, seguido de dois pontos, seguido do valor.

O rótulo "documents" recebe uma array de strings com os UID's referentes a cada documento solicitado pela organização que está criando a posição.

Exemplo do corpo da requisição em JSON:

```
{
  "num_matricula": "4242424",
  "limit_date": "2018-01-01",
  "admission_date": "2018-01-01",
  "cost_center": "anywhere",
  "pos_number": "pos-homer-test",
  "role": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "department": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "pagamento": {
    "vinculo": "clt",
    "valor": "4200",
    "recorrencia": "mensalista"
  },
  "deficiencia": false,
  "jornada": "De segunda a sexta das 15 as 19",
  "profile": {
    "name": "Joao Teste API",
    "email": "teste@acessodigital.com.br",
    "mobile": "11911111111"
  },
  "exame": {
    "data": "2018-01-01",
    "hora": "14:00",
    "obs": "Comparecer de manhã",
    "guia": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  },
  "docs": ["c9e26093-5e0c-4bd2-bea3-ac5182a6179f"],
  "send_sms": true,
  "send_email": true
}
```

Com o sucesso da requisição, o status da resposta será `201 Created` e seu body deverá conter um objeto JSON no seguinte formato:

```
{
  "code": 201,
  "messages": null,
  "result": {
    "invite": {
      "name": "Joao Teste API",
      "email": "teste@acessodigital.com.br",
      "mobile": {
        "countryCode": "55",
        "number": "11911111111"
      }
    },
    "position": "e8efddec-4a9e-4153-9e61-ce0056edf542"
  },
  "status": "ok"
}
```