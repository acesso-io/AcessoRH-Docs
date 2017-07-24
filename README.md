# **Acesso RH API**

 Plataforma da Acesso RH para consulta de cadastros concluídos.

## Índice

##### 1. [**Glossário**](#glossário)
##### 2. [**Recebendo notificação de posições concluídas**](#recebendo-notificação-de-posições-concluídas)
##### 3. [**Consultando uma posição**](#consultando-uma-posição)
##### 4. [**Consultando múltiplas posições**](#consultando-múltiplas-posições)
##### 5. [**Tokens de autenticação**](#token-de-autenticação)

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
: Json é uma formatação leve de troca de dados. Para seres humanos, é fácil de ler e escrever. Para máquinas, é fácil de interpretar e gerar. Está baseado em um subconjunto da linguagem de programação JavaScript.






### Recebendo notificação de posições concluídas

O Acesso RH disponibiliza uma plataforma para verificar a **conclusão** de um cadastro.
Para isto basta fornecer uma URL apta a receber uma requisição POST, com isto a API da acessoRH enviará a seguinte requisição:

```
POST curl 'http://{sua URL}' -d '{org:xxxx-xxxxx-xxxx-xxxx,acc:xxxx-xxxx-xxxx-xxxx,pos:xxxx-xxxx-xxxx-xxxx}'
```

Sempre que uma posição for concluída a API procurará as URLs cadastradas relativas a esta posição e enviará a informação por [chamada REST](#chamada_rest).

### Consultando uma posição

Após o recebimento da uid_pos, (UID da posição) é possível utilizá-la para fazer uma requisição, para isto:

```
curl -X GET  -H "Authorization: Bearer {token}" 'https://api.acessorh.com.br/v1/positions/{uid_pos}'
```

A requisição acima retornará um [Json](#json) com o seguinte valor da posição:

```go
HTTP/1.1 200 OK
Content-Type: application/json
{
  "_id":UUID("xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"),
  "profile":{
    "name":"xxx",
    "email":"xxx@yyy.com",
    "mobile":"11 12345 1234"
  },
  "sms_sent":"11 12345 1234",
  "email_sent":"xxx@yyy.com",
  "jornada":"Descrição da jornada",
  "acc":UUID("xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"),
  "status":240,
  "pagamento":{"vinculo":"clt","valor":"R$ 4.321,00"},
  "admission_date":"2018-08-08 00:00:00.000Z",
  "_created":"2017-07-17 19:03:24.216Z",
  "_updated":"2017-07-17 19:03:24.216Z"
}
```

Além destes campos, é possível acrescentar outros opcionais:

- docs: A lista de documentos enviados pelo usuário (cpf, rg, endereço, etc)
- role: O cargo que foi contratado
- account: A conta na qual a posição foi adicionada
- department: O Departamento

Caso queira acrescentar estes campos adicionais na resposta, basta inclui-los no request, colocando o parâmetro **include** da seguinte forma:

```
curl -X GET -H "Authorization: Bearer {token}" 'https://api.acessorh.com.br/v1/positions/{uid_pos}?include=docs,account,role,department'
```

Exemplos:

```
GET api/V1/positions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx?include=docs,account
```
```
GET api/V1/positions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx?include=department,role
```
Resultado em [Json](#json) de todos campos adicionais:

```go
HTTP/1.1 200 OK
Content-Type: application/json
{
  "account": {
    "id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
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
    "org": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
  },
  "admission_date": "2018-08-07T21:00:00-03:00",
  "department": {
    "account": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "name": "Depto Pessoal"
  },
  "docs": {
    "alistamento": {
      "data": {
        "id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "ra": "123123",
        "numero": "00000000000",
        "serie": null,
        "foto1": {
          "url": "https://xyz.com"
        },
        "foto2": {
          "url": "https://xyz.com"
        }
      },
      "valid": true,
      "name": "alistamento",
      "id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    },
    "banco": {
      "data": {
        "id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "banco": "xyz",
        "agencia": "0000",
        "conta": "00000",
        "digito": "0",
        "foto1": {
          "url": "https://xyz.com"
        }
      },
      "valid": true,
      "name": "banco",
      "id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    },
    "carteira_trabalho": {
      "data": {
        "id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "numero": "00000000000",
        "serie": "123",
        "uf": "SP",
        "data_emissao": "2017-01-10T22:00:00-02:00",
        "foto1": {
          "url": "https://xyz.com"
        },
        "foto2": {
          "url": "https://xyz.com"
        },
        "foto3": {
          "url": "https://xyz.com"
        }
      },
      "valid": true,
      "name": "carteira_trabalho",
      "id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    },
    "cnh": {
      "data": {
        "id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "numero": "00000000000",
        "validade": "2016-01-11T22:00:00-02:00",
        "data_emissao": "2016-01-11T22:00:00-02:00",
        "orgao_emissor": "SSPSP",
        "uf": "SP",
        "categoria": "AB",
        "remunerado": false,
        "foto1": {
          "url": "https://xyz.com"
        },
        "foto2": {
          "url": "https://xyz.com"
        }
      },
      "valid": true,
      "name": "cnh",
      "id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    },
    "cpf": {
      "data": {
        "id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "numero": "00000000000",
        "foto1": {
          "url": "https://xyz.com"
        }
      },
      "valid": true,
      "name": "cpf",
      "id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    },
    "endereco": {
      "data": {
        "id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "cep": "00000000",
        "logradouro": "Rua dos famosos",
        "numero": "313",
        "complemento": "00/0",
        "bairro": "Beverly Hills",
        "cidade": "Los Angeles",
        "uf": "LA",
        "foto1": {
          "url": "https://xyz.com"
        }
      },
      "valid": true,
      "name": "endereco",
      "id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    },
    "escolaridade": {
      "data": {
        "id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "instrucao": "superior-incompleto",
        "foto1": {
          "url": "https://xyz.com"
        }
      },
      "valid": true,
      "name": "escolaridade",
      "id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    },
    "nascimento": {
      "data": {
        "id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "foto1": {
          "url": "https://xyz.com"
        }
      },
      "valid": true,
      "name": "nascimento",
      "id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    },
    "pessoal": {
      "data": {
        "id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "nome": "xyz",
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
          "url": "https://xyz.com"
        }
      },
      "valid": true,
      "name": "pessoal",
      "id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    },
    "pis": {
      "data": {
        "id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "numero": "00000000",
        "foto1": {
          "url": "https://xyz.com"
        }
      },
      "valid": true,
      "name": "pis",
      "id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    },
    "rg": {
      "data": {
        "id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "numero": "0000000000",
        "orgao_emissor": "LA",
        "uf": "LA",
        "data_expedicao": "2016-01-11T22:00:00-02:00",
        "naturalidade_cidade": "Los Angeles",
        "naturalidade_estado": "LA",
        "foto1": {
          "url": "https://xyz.com"
        },
        "foto2": {
          "url": "https://xyz.com"
        }
      },
      "valid": true,
      "name": "rg",
      "id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    },
    "sindicato": {
      "data": {
        "id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "contribuinte": false,
        "foto1": {
          "url": "https://xyz.com"
        }
      },
      "valid": true,
      "name": "sindicato",
      "id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    },
    "sus": {
      "data": {
        "id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "numero": "00000000000",
        "foto1": {
          "url": "https://xyz.com"
        }
      },
      "valid": true,
      "name": "sus",
      "id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    },
    "titulo_eleitor": {
      "data": {
        "id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "numero": "00000000",
        "zona": "11",
        "secao": "000",
        "uf": "LA",
        "data_emissao": "2017-01-10T22:00:00-02:00",
        "foto1": {
          "url": "https://xyz.com"
        },
        "foto2": {
          "url": "https://xyz.com"
        }
      },
      "valid": true,
      "name": "titulo_eleitor",
      "id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    }
  },
  "exame": {
    "data": "2017-08-08T00:00:00Z",
    "hora": "2006-01-02T08:08:00Z",
    "comprovante": {
      "url": null
    },
    "clinica": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "observacoes": "Ligar e agendar"
  },
  "id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "jornada": "Trabalha cinco dias por semana",
  "pagamento": {
    "vinculo": "clt",
    "valor": "R$ 2.100,00",
    "recorrencia": "mensalista"
  },
  "profile": {
    "id": null,
    "email": "zzy@www.io",
    "mobile": "11 12345 12345",
    "name": "yyxz",
    "photo": {
      "url": null
    }
  },
  "role": {
    "account": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "name": "xyz"
  },
  "status": {
    "code": 400,
    "name": "active"
  },
  "updated": "2017-07-20T17:12:11.264-03:00"
}

```

### Consultando múltiplas posições

É possível consultar múltiplas posições relativas a uma conta ou organização (dependendo da permissão do token). Para isto enviar a seguinte requisição:

```
POST curl -H "Authorization: Bearer {token}" 'https://api.acessorh.com.br/v1/api/v1/positions'
```

Esta requisição responderá um [Json](#json) com objeto, cabeçalho e uma lista de posições:

```go
HTTP/1.1 200 OK
Content-Type: application/json
{
  "offset":0,
  "count":2,
  "positions":[
    {
      "_id":UUID("xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"),
      "profile":{
        "name":"xxx",
        "email":"xxx@yyy.com",
        "mobile":"11 12345 1234"
      },
      "sms_sent":"11 12345 1234",
      "email_sent":"xxx@yyy.com",
      "jornada":"Descrição da jornada",
      "acc":UUID("56786739-8916-7259-6730-815092597835"),
      "status":240,
      "pagamento":{"vinculo":"clt","valor":"R$ 4.321,00"},
      "admission_date":"2018-08-08 00:00:00.000Z",
      "_created":"2017-07-17 19:03:24.216Z",
      "_updated":"2017-07-17 19:03:24.216Z"
    },
    {
      "_id":UUID("xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"),
      "profile":{
        "name":"xyz",
        "email":"zzz@www.com",
        "mobile":"11 54321 4321"
      },
      "sms_sent":"11 54321 4321",
      "email_sent":"www@zzz.com",
      "jornada":"Descrição da jornada",
      "acc":UUID("12345678-1234-1234-1234-123456789123"),
      "status":240,
      "pagamento":{"vinculo":"clt","valor":"R$ 5.000,00"},
      "admission_date":"2017-05-08 00:00:00.000Z",
      "_created":"2017-07-19 19:03:24.216Z",
      "_updated":"2017-07-19 19:03:24.216Z"
    }
  ]
}
```
É possível filtrar a busca por **account**.
```
curl -X GET -H "Authorization: Bearer {token}" 'http://https://api.acessorh.com.br/v1/positions?acc=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'
```

Nestas buscas não pode ser passado como parâmetros os **includes**, sendo que estes apenas podem ser exibidos na busca por uma posição da account.

Exemplo:

```
curl -X GET -H "Authorization: Bearer {token}" 'https://api.acessorh.com.br/v1/positions?acc=12345678-1234-1234-1234-123456789123'
```
Resultado em [Json](#json):
```go
HTTP/1.1 200 OK
Content-Type: application/json
{
  "count":1,
  "offset":0,
  "positions":[
    {
      "_id":UUID("xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"),
      "profile":{
        "name":"xyz",
        "email":"zzz@www.com",
        "mobile":"11 54321 4321"
      },
      "sms_sent":"11 54321 4321",
      "email_sent":"www@zzz.com",
      "jornada":"Descrição da jornada",
      "acc":UUID("12345678-1234-1234-1234-123456789123"),
      "status":240,
      "pagamento":{"vinculo":"clt","valor":"R$ 5.000,00"},
      "admission_date":"2017-05-08 00:00:00.000Z",
      "_created":"2017-07-19 19:03:24.216Z",
      "_updated":"2017-07-19 19:03:24.216Z"
    }
  ]
}
```

### Consultando uma conta

Para consultar uma conta é necessário ... (texto)

```
curl -X GET  -H "Authorization: Bearer {token}" 'https://api.acessorh.com.br/v1/accounts/{uid_acc}'
```

Resultado da busca:

Resultado em [Json](#json):
```go
HTTP/1.1 200 OK
Content-Type: application/json
{
  "address": "Praça General Gentil Falcão",
  "address2": "cj 199/2",
  "brand": {
    "url": "xxxxxxx"
  },
  "city": "São Paulo",
  "cnpj": "xxxxxxxxx",
  "district": "Cidade Monções",
  "email": "acesso@io.com",
  "id": "2f23fff1-170b-4e7a-a74b-a79aa9dad418",
  "name": "Two and a Half",
  "org": UUID("xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"),
  "phone_number": "11 5678-1234",
  "state": "São Paulo",
  "trading_name": "Acesso IO LTDA",
  "zip_code": "02234120"
}
```

### Consultando multiplas contas

Para consultar uma conta é necessário ... (texto)

```
curl -X GET  -H "Authorization: Bearer {token}" 'https://api.acessorh.com.br/v1/accounts'

Resultado da busca:

Resultado em [Json](#json):
```go
HTTP/1.1 200 OK
Content-Type: application/json
[
 {
   "address": "Praça General Gentil Falcão",
   "address2": "cj 199/2",
   "brand": {
     "url": "xxxxxxx"
   },
   "city": "São Paulo",
   "cnpj": "xxxxxxxxx",
   "district": "Cidade Monções",
   "email": "acesso@io.com",
   "id": "2f23fff1-170b-4e7a-a74b-a79aa9dad418",
   "name": "Two and a Half",
   "org": UUID("xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"),
   "phone_number": "11 5678-1234",
   "state": "São Paulo",
   "trading_name": "Acesso IO LTDA",
   "zip_code": "02234120"
 }
]
```
```

### Ativação da API

A ativação da API é feita via dashboard. Clicando em empresas e na aba API preencha o formulário para a geração de um token de acesso. Para este formulario é necessário preencher as seguintes informaçes:
- Contas: As contas que poderão ser acessadas, e as respectivas posições
- Duração Token: O tempo de expiração do token

Após o preenchimento é gerado um refresh token. Este refresh token é utilizado como uma pré autenticação na API, sendo necessário fazer uma requisição pra adquirir o token final:

```
curl -X GET  -H "Authorization: Bearer {ref_token}" 'https://api.acessorh.com.br/v1/auth'
```

A resposta trás o token e a data de expiração:

Resultado em [Json](#json):
```go
HTTP/1.1 200 OK
Content-Type: application/json
{
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.e30.t-IDcSemACt8x4iTMCda8Yhe3iZaWbvV5XKSTbuAn0M",
  "exp": "2017-05-08 00:00:00.000Z",
}
```

Este é o token usado para fazer as chamadas REST durante seu tempo de validade. Quando o token expirar, é necessário fazer outra requisição ao endpoint de autenticação pra adquiririr um token novo.
