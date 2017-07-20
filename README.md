# **AcessoRH API**

#### Plataforma da AcessoRH para consulta de cadastros concluídos.


## Índice

##### 1. [**Recebendo notificação de posições concluídas**](#recebendo-notificação-de-posições-concluídas)
##### 2. [**Consultando uma posição**](#consultando-uma-posição)
##### 3. [**Consultando múltiplas requisições**](#consultando-múltiplas-requisições)
##### 4. [**Token de autenticação**](#token-de-autenticação)


### Recebendo notificação de posições concluídas

O AcessoRH disponibiliza uma plataforma para verificar a **conclusão** de um cadastro.
Para isto basta fornecer uma URL apta a receber uma requisição POST, com isto a API da acessoRH enviará a seguinte requisição:

```
POST curl 'http://{sua URL}' -d '{org:xxxx-xxxxx-xxxx-xxxx,acc:xxxx-xxxx-xxxx-xxxx,pos:xxxx-xxxx-xxxx-xxxx}'
```

Sempre que uma posição for concluída a API procurará as URLs cadastradas relativas a esta posição e enviará a informação por chamada REST.

### Consultando uma posição

Após o recebimento da uid_pos, (UID da posição) é possível utilizá-la para fazer uma requisição, para isto:

```
POST curl -H "Authorization: Bearer {token}" 'http://www.acessorh.com.br/api/v1/position/{uid_pos}'
```

A requisição acima retornará um Json com o seguinte valor da posição:

```go
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
POST curl -H "Authorization: Bearer {token}" 'http://www.acessorh.com.br/api/v1/positions/{uid_pos}?include=docs,account,role,department'
```

Exemplos:

```
GET api/V1/position/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx?include=docs,account
```
```
GET api/V1/position/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx?include=department,role
```
Resultado em Json de todos campos adicionais:

```go
{
  "account": {
    "id": "4573eec6-25b8-41f9-bb49-9a5c3fcb9fff",
    "name": "Two and a Half",
    "trading_name": "Two and a Half CO",
    "phone_number": "11 5678-1234",
    "email": "twoAndAHalf@bros.io",
    "zip_code": "02234120",
    "address": "Praça General Gentil Falcão",
    "address2": null,
    "state": "São Paulo",
    "city": "São Paulo",
    "district": "Cidade Monções",
    "brand": {
      "url": "https://storage.googleapis.com/acessorh-dev/doc/br/5d2c6708-e996-4cc7-ac17-f8c067088cc1/e600194d-5f9d-4d73-bbc8-09b3a21cf81d/a8a89892-c9fd-40df-8dfd-5fa694c59ddf/83c53605-5f72-4b9b-b5ae-1f49f74409a0?Expires=1500669724&GoogleAccessId=acessorh-public-files%40acesso-io.iam.gserviceaccount.com&Signature=tqxxSsIfexnEjRplc5qMdn3klscKQqv4Ob%2Fi4i%2F3oGZqUh6RU%2FgzyQrHS7Nk%2B2wIuoUnppQwhQmGFxcgbSozjPvQhJgXcrENn8qvYxD8sEQXOH1se7V3j2rMO3xGpqls28bu%2Bo80xos4bXF6N4kL%2FVzLDncwAzOu1LYBrb%2FF15r4CUsxBCbXLNuu%2FWKuT8if5%2FsxvwNlDMK2f87VNj%2BECGUSOnYlky8qUU7Hv2l6jlyRaWJyaHjguLMqkyDiqidfRZtKaWeCOW6RGKTIy9Vcw6fGA3r%2F5Jc5d6L643oCnjjbnqAxZc3k72P2fOegQiEDQlrC6I20fl8PMbcX0af5nQ%3D%3D"
    },
    "cnpj": "",
    "org": "4be06c05-8fd6-4ef2-a2dd-ac78a4d29953"
  },
  "admission_date": "2018-08-07T21:00:00-03:00",
  "department": {
    "account": "4573eec6-25b8-41f9-bb49-9a5c3fcb9fff",
    "id": "84584af8-194a-4f57-99bd-5c72cbd7823f",
    "name": "Depto Pessoal"
  },
  "docs": {
    "alistamento": {
      "data": {
        "id": "5bb67d91-99ef-469c-ae73-f51498621acb",
        "ra": "123123",
        "numero": "88732218311",
        "serie": null,
        "foto1": {
          "url": "xxx"
        },
        "foto2": {
          "url": "xxx"
        }
      },
      "valid": true,
      "name": "alistamento",
      "id": "5bb67d91-99ef-469c-ae73-f51498621acb"
    },
    "banco": {
      "data": {
        "id": "a8a89892-c9fd-40df-8dfd-5fa694c59ddf",
        "banco": "Itaú",
        "agencia": "0399",
        "conta": "54990",
        "digito": "6",
        "foto1": {
          "url": "xxx"
        }
      },
      "valid": true,
      "name": "banco",
      "id": "a8a89892-c9fd-40df-8dfd-5fa694c59ddf"
    },
    "carteira_trabalho": {
      "data": {
        "id": "0f98ddee-7bb0-4b32-a849-99d297202a81",
        "numero": "88732218311",
        "serie": "123",
        "uf": "SP",
        "data_emissao": "2017-01-10T22:00:00-02:00",
        "foto1": {
          "url": "https://xxx"
        },
        "foto2": {
          "url": "https://xxx"
        },
        "foto3": {
          "url": "https://xxx"
        }
      },
      "valid": true,
      "name": "carteira_trabalho",
      "id": "0f98ddee-7bb0-4b32-a849-99d297202a81"
    },
    "cnh": {
      "data": {
        "id": "b508549a-c45a-425f-8773-d60dc7484750",
        "numero": "88732218311",
        "validade": "2016-01-11T22:00:00-02:00",
        "data_emissao": "2016-01-11T22:00:00-02:00",
        "orgao_emissor": "SSPSP",
        "uf": "SP",
        "categoria": "AB",
        "remunerado": false,
        "foto1": {
          "url": "https://xxx"
        },
        "foto2": {
          "url": "https://xxx"
        }
      },
      "valid": true,
      "name": "cnh",
      "id": "b508549a-c45a-425f-8773-d60dc7484750"
    },
    "cpf": {
      "data": {
        "id": "6d58e6f3-5501-40b3-b1fb-3e6949d19960",
        "numero": "89554885210",
        "foto1": {
          "url": "https://xxx"
        }
      },
      "valid": true,
      "name": "cpf",
      "id": "6d58e6f3-5501-40b3-b1fb-3e6949d19960"
    },
    "endereco": {
      "data": {
        "id": "7d5eb9da-b50e-49f7-8c3e-3ee5877eb620",
        "cep": "05728030",
        "logradouro": "Rua das cacetas",
        "numero": "313",
        "complemento": "199/1",
        "bairro": "Beverly Hills",
        "cidade": "Los Angeles",
        "uf": "LA",
        "foto1": {
          "url": "https://xxx"
        }
      },
      "valid": true,
      "name": "endereco",
      "id": "7d5eb9da-b50e-49f7-8c3e-3ee5877eb620"
    },
    "escolaridade": {
      "data": {
        "id": "93c3527c-1bed-45d0-8725-1705e580357d",
        "instrucao": "superior-incompleto",
        "foto1": {
          "url": "https://xxx"
        }
      },
      "valid": true,
      "name": "escolaridade",
      "id": "93c3527c-1bed-45d0-8725-1705e580357d"
    },
    "nascimento": {
      "data": {
        "id": "35e0ebd9-c248-4dff-b671-54ffa8a46501",
        "foto1": {
          "url": "https://xxx"
        }
      },
      "valid": true,
      "name": "nascimento",
      "id": "35e0ebd9-c248-4dff-b671-54ffa8a46501"
    },
    "pessoal": {
      "data": {
        "id": "ce5fda44-fbad-4dbd-84de-2c30d110e71e",
        "nome": "Charlie Harper",
        "nome_social": null,
        "nome_mae": "Evelyn Harper",
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
          "url": "https://xxx"
        }
      },
      "valid": true,
      "name": "pessoal",
      "id": "ce5fda44-fbad-4dbd-84de-2c30d110e71e"
    },
    "pis": {
      "data": {
        "id": "4c92e63b-5e92-4499-979f-4f3e233d596e",
        "numero": "77848548",
        "foto1": {
          "url": "https://xxx"
        }
      },
      "valid": true,
      "name": "pis",
      "id": "4c92e63b-5e92-4499-979f-4f3e233d596e"
    },
    "rg": {
      "data": {
        "id": "c2069ecf-ea5d-4029-9960-6f802392c6d7",
        "numero": "2910001887",
        "orgao_emissor": "LA",
        "uf": "LA",
        "data_expedicao": "2016-01-11T22:00:00-02:00",
        "naturalidade_cidade": "Los Angeles",
        "naturalidade_estado": "LA",
        "foto1": {
          "url": "https://xxx"
        },
        "foto2": {
          "url": "https://xxx"
        }
      },
      "valid": true,
      "name": "rg",
      "id": "c2069ecf-ea5d-4029-9960-6f802392c6d7"
    },
    "sindicato": {
      "data": {
        "id": "e2a41d40-f60b-48fa-80c5-87812e165fbe",
        "contribuinte": false,
        "foto1": {
          "url": "https://xxx"
        }
      },
      "valid": true,
      "name": "sindicato",
      "id": "e2a41d40-f60b-48fa-80c5-87812e165fbe"
    },
    "sus": {
      "data": {
        "id": "32e00f6d-e847-4ac8-be6e-13b8c36b7a27",
        "numero": "88732218311",
        "foto1": {
          "url": "https://xxx"
        }
      },
      "valid": true,
      "name": "sus",
      "id": "32e00f6d-e847-4ac8-be6e-13b8c36b7a27"
    },
    "titulo_eleitor": {
      "data": {
        "id": "7923a0f1-170a-417b-a2c7-bdad95ee4fdc",
        "numero": "453253665",
        "zona": "11",
        "secao": "331",
        "uf": "LA",
        "data_emissao": "2017-01-10T22:00:00-02:00",
        "foto1": {
          "url": "https://xxx"
        },
        "foto2": {
          "url": "https://xxx"
        }
      },
      "valid": true,
      "name": "titulo_eleitor",
      "id": "7923a0f1-170a-417b-a2c7-bdad95ee4fdc"
    }
  },
  "exame": {
    "data": "2017-08-08T00:00:00Z",
    "hora": "2006-01-02T08:08:00Z",
    "comprovante": {
      "url": null
    },
    "clinica": "8ae3c455-eb0c-413c-b962-62acfcacebd8",
    "observacoes": "Ligar e agendar"
  },
  "id": "e600194d-5f9d-4d73-bbc8-09b3a21cf81d",
  "jornada": "Trabalha uma vez por semana e fatura milhões",
  "pagamento": {
    "vinculo": "clt",
    "valor": "R$ 2.100,00",
    "recorrencia": "mensalista"
  },
  "profile": {
    "id": null,
    "email": "charlie@twoAndAHalf.io",
    "mobile": "11 12345 12345",
    "name": "Charlie Harper",
    "photo": {
      "url": null
    }
  },
  "role": {
    "account": "4573eec6-25b8-41f9-bb49-9a5c3fcb9fff",
    "id": "a5821584-ca63-441b-9fbe-77d3e8489f1c",
    "name": "Matador de aluguel"
  },
  "status": {
    "code": 400,
    "name": "active"
  },
  "updated": "2017-07-20T17:12:11.264-03:00"
}
```

### Consultando múltiplas requisições

É possível consultar múltiplas posições relativas a uma conta ou organização (dependendo da permissão do token). Para isto enviar a seguinte requisição:

```
POST curl -H "Authorization: Bearer {token}" 'http://www.acessorh.com.br/api/v1/positions'
```

Esta requisição responderá um Json com objeto, cabeçalho e uma lista de posições:

```go
{
"count":2,
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
  "acc":UUID("xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"),
  "status":240,
  "pagamento":{"vinculo":"clt","valor":"R$ 5.000,00"},
  "admission_date":"2017-05-08 00:00:00.000Z",
  "_created":"2017-07-19 19:03:24.216Z",
  "_updated":"2017-07-19 19:03:24.216Z"
}
```
É possível filtrar a busca por **account**.
```
POST curl -H "Authorization: Bearer {token}" 'http://www.acessorh.com.br/api/v1/positions?acc=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'
```

Nestas buscas não pode ser passado como parâmetros os **includes**, sendo que estes apenas podem ser exibidos na busca por uma posição da account.

### Token de autenticação

Os tokens de autenticação são fixos e com data de validade de um ano. Eles podem ter dois tipos de roles:

- Conta: Permite usar a API apenas para consulta interna de uma conta, ou seja, apenas às posições relativas àquela conta.
- Organização: Permite uma visualização ampla.
