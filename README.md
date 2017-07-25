# **Acesso RH API**

 Plataforma da Acesso RH para consulta de cadastros concluídos.

## Índice

##### 1. [**Ativação da API**](#ativação-da-api)
##### 2. [**Recebendo notificação de posições concluídas**](#recebendo-notificação-de-posições-concluídas)
##### 3. [**Consultando uma posição**](#consultando-uma-posição)
##### 4. [**Consultando múltiplas posições**](#consultando-múltiplas-posições)
##### 5. [**Consultando multiplas contas**](#consultando-multiplas-contas)
##### 6. [**Consultando uma conta**](#consultando-uma-conta)
##### 7. [**Glossário**](#glossário)


### Ativação da API

A ativação da API é feita via dashboard. Clique em empresas e na aba API preencha o formulário para a geração de um [token](#token) de acesso. Para este formulário é necessário preencher as seguintes informações:

- Contas: As contas que poderão ser acessadas, e as respectivas posições.
- Duração do Token: O tempo de expiração do token.

Após o preenchimento é gerado um [refresh token](#refresh_token). Este [refresh token](#refresh_token) é utilizado como uma pré autenticação na API, sendo necessário fazer uma requisição para adquirir o [token](#token) final, ex.:

```
curl -X GET  -H "Authorization: Bearer {refresh token}" 'https://api.acessorh.com.br/v1/auth'
```

A resposta traz o [token](#token) final e a data de expiração em [JSON](#json):


```
HTTP/1.1 200 OK
Content-Type: application/json
{
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.e30.t-IDcSemACt8x4iTMCda8Yhe3iZaWbvV5XKSTbuAn0M",
  "exp": "1500933544"
}
```

Este é o token usado para fazer as [chamadas REST](#chamada_rest) durante seu tempo de validade. Quando o [token](#token) expirar, é necessário fazer outra requisição utilizando um [refresh token](#refresh_token).


### Recebendo notificação de posições concluídas

O Acesso RH disponibiliza de um sistema de notificação **PUSH** para cadastros **concluídos**.
Sempre que uma posição for concluída, a plataforma dispara uma notificação **PUSH** para o endereço configurado no painel.

No painel administrativo é possível cadastrar uma URL para receber as notificações (ex.: https://www.meu-site.com.br/acesso-rh/notificacao), uma vez cadastrado, toda nova conclusão de cadastro resultará em uma requisição como esta:

```
POST {url especificada} HTTP/1.1
Content-Type: application/json;

{
  "org": "xxxx-xxxxx-xxxx-xxxx",
  "acc": "xxxx-xxxx-xxxx-xxxx",
  "pos": "xxxx-xxxx-xxxx-xxxx"
}
```

A Plataform espera receber o seguinte recibo de entrega com sucesso:

```
HTTP/1.1 200 OK
```

Caso a URL configurada para **PUSH** não responda com o recibo de entrega com sucesso será efetuado um agendamento para um nova tentativa de entrega, esse processo será repetido até que retorne o recibo esperado (limitado a um numero máximo de notificações).

### Consultando uma posição

Após o recebimento da notificação, é possível utilizar os dados recebidos para fazer uma consulta na plataforma, para isto, basta fazer a requisição abaixo, porém informando na URL o valor recebido referente ao campo `pos`:

```
curl -X GET  -H "Authorization: Bearer {token}" 'https://api.acessorh.com.br/v1/positions/{pos}'
```

A requisição acima retornará um [JSON](#json) conforme o exemplo abaixo:

```
HTTP/1.1 200 OK
Content-Type: application/json
{
  "_id": "22a2ceb7-418f-4501-aa91-52064f269743",
  "profile": {
    "name": "xxx",
    "email": "acesso@io.com",
    "mobile": "11 12345 1234"
  },
  "sms_sent": "11 12345 1234",
  "email_sent": "acesso@io.com",
  "jornada": "Descrição da jornada",
  "acc": "22a2ceb7-418f-4501-aa91-52064f269743",
  "status": 240,
  "pagamento": { "vinculo": "clt", "valor": "R$ 4.321,00" },
  "admission_date": "2018-08-08 00:00:00.000Z",
  "_created": "2017-07-17 19:03:24.216Z",
  "_updated": "2017-07-17 19:03:24.216Z"
}
```

Além destes campos, é possível solicitar outros adicionais:

- `docs`: A lista de documentos enviados pelo usuário (CPF, RG, etc...)
- `account`: Os detalhes da conta na qual a posição foi criada
- `department`: Os detalhes do departamento
- `role`: Os detalhes do cargo

Caso queira acrescentar estes campos adicionais na resposta, basta inclui-los na solicitação informando o parâmetro **include**, da seguinte forma:

```
curl -X GET -H "Authorization: Bearer {token}" 'https://api.acessorh.com.br/v1/positions/{pos}?include=docs,account,role,department'
```

Exemplos:

```
GET api/V1/positions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx?include=docs,account
```

```
GET api/V1/positions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx?include=department,role
```

Exemplo de resultado em [JSON](#json) contendo todos os campos adicionais:

```
HTTP/1.1 200 OK
Content-Type: application/json
{
  "account": {
    "id": "22a2ceb7-418f-4501-aa91-52064f269743",
    "name": "zyx",
    "trading_name": "xyz CO",
    "phone_number": "11 xxxx-xxxx",
    "email": "comercial@acesso.io",
    "zip_code": "00000000",
    "address": "Praça General Gentil Falcão",
    "address2": null,
    "state": "São Paulo",
    "city": "São Paulo",
    "district": "Cidade Monções",
    "brand": {
      "url": "https://api.acessorh.com.br/v1/files/caminho-do-arquivo.jpg"
    },
    "cnpj": "",
    "org": "22a2ceb7-418f-4501-aa91-52064f269743"
  },
  "admission_date": "2018-08-07T21:00:00-03:00",
  "department": {
    "account": "22a2ceb7-418f-4501-aa91-52064f269743",
    "id": "22a2ceb7-418f-4501-aa91-52064f269743",
    "name": "Depto Pessoal"
  },
  "docs": {
    "alistamento": {
      "id": "22a2ceb7-418f-4501-aa91-52064f269743",
      "ra": "123123",
      "numero": "00000000000",
      "serie": null,
      "foto1": {
        "url": "https://api.acessorh.com.br/v1/files/caminho-do-arquivo.jpg"
      },
      "foto2": {
        "url": "https://api.acessorh.com.br/v1/files/caminho-do-arquivo.jpg"
      }
    },
    "banco": {
      "id": "22a2ceb7-418f-4501-aa91-52064f269743",
      "banco": "xyz",
      "agencia": "0000",
      "conta": "00000",
      "digito": "0",
      "foto1": {
        "url": "https://api.acessorh.com.br/v1/files/caminho-do-arquivo.jpg"
      }
    },
    "carteira_trabalho": {
      "id": "22a2ceb7-418f-4501-aa91-52064f269743",
      "numero": "00000000000",
      "serie": "123",
      "uf": "SP",
      "data_emissao": "2017-01-10T22:00:00-02:00",
      "foto1": {
        "url": "https://api.acessorh.com.br/v1/files/caminho-do-arquivo.jpg"
      },
      "foto2": {
        "url": "https://api.acessorh.com.br/v1/files/caminho-do-arquivo.jpg"
      },
      "foto3": {
        "url": "https://api.acessorh.com.br/v1/files/caminho-do-arquivo.jpg"
      }
    },
    "cnh": {
      "id": "22a2ceb7-418f-4501-aa91-52064f269743",
      "numero": "00000000000",
      "validade": "2016-01-11T22:00:00-02:00",
      "data_emissao": "2016-01-11T22:00:00-02:00",
      "orgao_emissor": "SSPSP",
      "uf": "SP",
      "categoria": "AB",
      "remunerado": false,
      "foto1": {
        "url": "https://api.acessorh.com.br/v1/files/caminho-do-arquivo.jpg"
      },
      "foto2": {
        "url": "https://api.acessorh.com.br/v1/files/caminho-do-arquivo.jpg"
      }
    },
    "cpf": {
      "id": "22a2ceb7-418f-4501-aa91-52064f269743",
      "numero": "00000000000",
      "foto1": {
        "url": "https://api.acessorh.com.br/v1/files/caminho-do-arquivo.jpg"
      }
    },
    "endereco": {
      "id": "22a2ceb7-418f-4501-aa91-52064f269743",
      "cep": "00000000",
      "logradouro": "Rua dos famosos",
      "numero": "313",
      "complemento": "00/0",
      "bairro": "Beverly Hills",
      "cidade": "Los Angeles",
      "uf": "LA",
      "foto1": {
        "url": "https://api.acessorh.com.br/v1/files/caminho-do-arquivo.jpg"
      }
    },
    "escolaridade": {
      "id": "22a2ceb7-418f-4501-aa91-52064f269743",
      "instrucao": "superior-incompleto",
      "foto1": {
        "url": "https://api.acessorh.com.br/v1/files/caminho-do-arquivo.jpg"
      }
    },
    "nascimento": {
      "id": "22a2ceb7-418f-4501-aa91-52064f269743",
      "foto1": {
        "url": "https://api.acessorh.com.br/v1/files/caminho-do-arquivo.jpg"
      }
    },
    "pessoal": {
      "id": "22a2ceb7-418f-4501-aa91-52064f269743",
      "nome": "Fulano de Tal",
      "nome_social": null,
      "nome_mae": "zyx",
      "nome_pai": null,
      "phone_number": null,
      "nascimento": "1972-01-25T21:00:00-03:00",
      "sexo": "masculino",
      "nacionalidade": "brasileiro",
      "pais_nascimento": null,
      "etnia": "branco",
      "estado_civil": "solteiro",
      "deficiencia": false,
      "deficiencias": null,
      "cnh": true,
      "dependente": false,
      "primeiro_emprego": true,
      "foto": {
        "url": "https://api.acessorh.com.br/v1/files/caminho-do-arquivo.jpg"
      }
    },
    "pis": {
      "id": "22a2ceb7-418f-4501-aa91-52064f269743",
      "numero": "00000000",
      "foto1": {
        "url": "https://api.acessorh.com.br/v1/files/caminho-do-arquivo.jpg"
      }
    },
    "rg": {
      "id": "22a2ceb7-418f-4501-aa91-52064f269743",
      "numero": "0000000000",
      "orgao_emissor": "LA",
      "uf": "LA",
      "data_expedicao": "2016-01-11T22:00:00-02:00",
      "naturalidade_cidade": "Los Angeles",
      "naturalidade_estado": "LA",
      "foto1": {
        "url": "https://api.acessorh.com.br/v1/files/caminho-do-arquivo.jpg"
      },
      "foto2": {
        "url": "https://api.acessorh.com.br/v1/files/caminho-do-arquivo.jpg"
      }
    },
    "sindicato": {
      "id": "22a2ceb7-418f-4501-aa91-52064f269743",
      "contribuinte": false,
      "foto1": {
        "url": "https://api.acessorh.com.br/v1/files/caminho-do-arquivo.jpg"
      }
    },
    "sus": {
      "id": "22a2ceb7-418f-4501-aa91-52064f269743",
      "numero": "00000000000",
      "foto1": {
        "url": "https://api.acessorh.com.br/v1/files/caminho-do-arquivo.jpg"
      }
    },
    "titulo_eleitor": {
      "id": "22a2ceb7-418f-4501-aa91-52064f269743",
      "numero": "00000000",
      "zona": "11",
      "secao": "000",
      "uf": "LA",
      "data_emissao": "2017-01-10T22:00:00-02:00",
      "foto1": {
        "url": "https://api.acessorh.com.br/v1/files/caminho-do-arquivo.jpg"
      },
      "foto2": {
        "url": "https://api.acessorh.com.br/v1/files/caminho-do-arquivo.jpg"
      }
    }
  },
  "exame": {
    "data": "2017-08-08T00:00:00Z",
    "hora": "2006-01-02T08:08:00Z",
    "comprovante": {
      "url": null
    },
    "clinica": "22a2ceb7-418f-4501-aa91-52064f269743",
    "observacoes": "Ligar e agendar"
  },
  "id": "22a2ceb7-418f-4501-aa91-52064f269743",
  "jornada": "Trabalha cinco dias por semana",
  "pagamento": {
    "vinculo": "clt",
    "valor": "R$ 2.100,00",
    "recorrencia": "mensalista"
  },
  "profile": {
    "id": null,
    "email": "contato@acesso.io",
    "mobile": "11 12345 12345",
    "name": "Fulano",
    "photo": {
      "url": null
    }
  },
  "role": {
    "account": "22a2ceb7-418f-4501-aa91-52064f269743",
    "id": "22a2ceb7-418f-4501-aa91-52064f269743",
    "name": "Assistente administrativo"
  },
  "status": {
    "code": 400,
    "name": "active"
  },
  "_updated": "2017-07-20T17:12:11.264-03:00",
  "_created": "2017-07-20T17:12:11.264-03:00"
}
```

### Consultando múltiplas posições

É possível consultar múltiplas posições relativas a uma conta ou organização (dependendo da permissão configurada).
Para isto enviar a seguinte requisição:

```
curl -X GET -H "Authorization: Bearer {token}" 'https://api.acessorh.com.br/v1/api/v1/positions'
```

Esta requisição responderá um [JSON](#json) com objeto, cabeçalho e uma lista de posições:

```go
HTTP/1.1 200 OK
Content-Type: application/json
{
  "offset": 0,
  "count": 2,
  "positions": [
    {
      "_id":"22a2ceb7-418f-4501-aa91-52064f269743",
      "profile":{
        "name":"xxx",
        "email":"acesso@io.com",
        "mobile":"11 12345 1234"
      },
      "sms_sent":"11 12345 1234",
      "email_sent":"acesso@io.com",
      "jornada":"Descrição da jornada",
      "acc":"22a2ceb7-418f-4501-aa91-52064f269743",
      "status":240,
      "pagamento":{"vinculo":"clt","valor":"R$ 4.321,00"},
      "admission_date":"2018-08-08 00:00:00.000Z",
      "_created":"2017-07-17 19:03:24.216Z",
      "_updated":"2017-07-17 19:03:24.216Z"
    },
    {
      "_id": ("22a2ceb7-418f-4501-aa91-52064f269743"),
      "profile":{
        "name":"xyz",
        "email":"acesso@io.com",
        "mobile":"11 54321 4321"
      },
      "sms_sent":"11 54321 4321",
      "email_sent":"acesso@io.com",
      "jornada":"Descrição da jornada",
      "acc":"22a2ceb7-418f-4501-aa91-52064f269743",
      "status":240,
      "pagamento":{"vinculo":"clt","valor":"R$ 5.000,00"},
      "admission_date":"2017-05-08 00:00:00.000Z",
      "_created":"2017-07-19 19:03:24.216Z",
      "_updated":"2017-07-19 19:03:24.216Z"
    }
  ]
}
```

É possível filtrar a busca por **contas**:

```
curl -X GET -H "Authorization: Bearer {token}" 'http://https://api.acessorh.com.br/v1/positions?acc=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'
```

Nestas buscas não pode ser passado como parâmetros os **includes**, sendo que estes apenas podem ser exibidos na busca por uma posição da account.

Exemplo:

```
curl -X GET -H "Authorization: Bearer {token}" 'https://api.acessorh.com.br/v1/positions?acc=12345678-1234-1234-1234-123456789123'
```
Resultado em [JSON](#json):
```go
HTTP/1.1 200 OK
Content-Type: application/json
{
  "count":1,
  "offset":0,
  "positions":[
    {
      "_id":"22a2ceb7-418f-4501-aa91-52064f269743",
      "profile":{
        "name":"xyz",
        "email":"acesso@io.com",
        "mobile":"11 54321 4321"
      },
      "sms_sent":"11 54321 4321",
      "email_sent":"acesso@io.com",
      "jornada":"Descrição da jornada",
      "acc":"22a2ceb7-418f-4501-aa91-52064f269743",
      "status":240,
      "pagamento":{"vinculo":"clt","valor":"R$ 5.000,00"},
      "admission_date":"2017-05-08 00:00:00.000Z",
      "_created":"2017-07-19 19:03:24.216Z",
      "_updated":"2017-07-19 19:03:24.216Z"
    }
  ]
}
```
### Consultando múltiplas contas

Para consultar múltiplas contas é necessário um [token](#token) com permissão, que retornara as **contas da organização**.

```
curl -X GET  -H "Authorization: Bearer {token}" 'https://api.acessorh.com.br/v1/accounts'
```


Resultado em [JSON](#json):

```go
HTTP/1.1 200 OK
Content-Type: application/json
[
 {
   "address": "Praça General Gentil Falcão",
   "address2": "000",
   "brand": {
     "url": "https://api.acessorh.com.br/v1/files/caminho-do-arquivo.jpg"
   },
   "city": "São Paulo",
   "cnpj": "xxxxxxxxx",
   "district": "Cidade Monções",
   "email": "acesso@io.com",
   "id": "2f23fff1-170b-4e7a-a74b-a79aa9dad418",
   "name": "AcessoBIO",
   "org": "22a2ceb7-418f-4501-aa91-52064f269743",
   "phone_number": "00 00000-0000",
   "state": "São Paulo",
   "trading_name": "Acesso IO LTDA",
   "zip_code": "0000000"
 },
 {
   "address": "Praça General Gentil Falcão",
   "address2": "000",
   "brand": {
     "url": "https://api.acessorh.com.br/v1/files/caminho-do-arquivo.jpg"
   },
   "city": "São Paulo",
   "cnpj": "xxxxxxxxx",
   "district": "Cidade Monções",
   "email": "acesso@io.com",
   "id": "5f23faf1-170b-4f7a-a74b-a79cd9dad348",
   "name": "AcessoRH",
   "org": "22a2ceb7-418f-4501-aa91-52064f269743",
   "phone_number": "00 00000-0000",
   "state": "São Paulo",
   "trading_name": "Acesso IO LTDA",
   "zip_code": "00000000"
 }
]
```

### Consultando uma conta

Para consultar uma conta é necessário um [token](#token) com permissão, e o UID de uma conta específica.

```
curl -X GET  -H "Authorization: Bearer {token}" 'https://api.acessorh.com.br/v1/accounts/{acc}'
```

Resultado em [JSON](#json):
```go
HTTP/1.1 200 OK
Content-Type: application/json
{
  "address": "Praça General Gentil Falcão",
  "address2": "cj 123/4",
  "brand": {
    "url": "https://api.acessorh.com.br/v1/files/caminho-do-arquivo.jpg"
  },
  "city": "São Paulo",
  "cnpj": "xxxxxxxxx",
  "district": "Cidade Monções",
  "email": "contato@acesso.io",
  "id": "2f23fff1-170b-4e7a-a74b-a79aa9dad418",
  "name": "Acesso",
  "org": "22a2ceb7-418f-4501-aa91-52064f269743",
  "phone_number": "00 00000-0000",
  "state": "São Paulo",
  "trading_name": "Acesso SA LTDA",
  "zip_code": "00000000"
}
```

### Glossário

<a id="chamada_rest">Chamada REST:</a>
: Na chamada REST as informações são acessadas através de endpoints em formato HTTPS, utilizando os seguintes comandos para realizar as ações:


|Comandos|Descrição|
|--------|-----|
|GET     |Usado para retornar informação.
|POST    |Usado para criar informação.
|PUT     |Usado para alterar/repor informação.
|DELETE  |Usado para deletar informação.


<a id="json">JSON:</a>
: JSON é uma formatação leve de troca de dados. Para seres humanos, é fácil de ler e escrever. Para máquinas, é fácil de interpretar e gerar. Está baseado em um subconjunto da linguagem de programação JavaScript.

<a id="timestamps">Timestamps</a>
:  O formato de data e hora que utilizamos é o padrão [RFC3339](https://www.ietf.org/rfc/rfc3339.txt) com fuso horário em UTC. Ex.:

```json
"2005-10-30 10:45:00.000"
```

<a id="token">Token</a>
: O Token é uma chave de acesso gerada apartir de um Refresh Token. Através do Token é possivel acessar a API e realizar [chamadas REST](#chamada_rest).

<a id="refresh_token">Refresh Token</a>
: Refresh Token é um token especial gerado no dashboard da organização, que contem as informações necessárias para gerar um token de acesso. 
