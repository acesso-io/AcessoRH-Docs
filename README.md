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
- Duração Token: O tempo de expiração do token.

Após o preenchimento é gerado um [refresh token](#refresh_token). Este [refresh token](#refresh_token) é utilizado como uma pré autenticação na API, sendo necessário fazer uma requisição para adquirir o [token](#token) final:

```
curl -X GET  -H "Authorization: Bearer {ref_token}" 'https://api.acessorh.com.br/v1/auth'
```

A resposta traz o [token](#token) final e a data de expiração em [JSON](#json):


```go
HTTP/1.1 200 OK
Content-Type: application/json
{
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.e30.t-IDcSemACt8x4iTMCda8Yhe3iZaWbvV5XKSTbuAn0M",
  "exp": "1500933544",
}
```

Este é o token usado para fazer as [chamadas REST](#chamada_rest) durante seu tempo de validade. Quando o [token](#token) expirar, é necessário fazer outra requisição utilizando um [refresh token](#refresh_token).



### Recebendo notificação de posições concluídas

O Acesso RH disponibiliza uma plataforma para verificar a **conclusão** de um cadastro.
Para isto basta fornecer uma URL apta a receber uma requisição POST, com isto a API da acesso RH enviará a seguinte requisição para a URL do cliente:

```
POST {sua URL} -d '{org:xxxx-xxxxx-xxxx-xxxx,acc:xxxx-xxxx-xxxx-xxxx,pos:xxxx-xxxx-xxxx-xxxx}'
```

Se espera receber o seguinte resultado do cliente:

```go
HTTP/1.1 200 OK
Content-Type: application/json
```

Sempre que uma posição for concluída a API procurará as URL’s cadastradas relativas a esta posição e enviará a informação por [chamada REST](#chamada_rest).

### Consultando uma posição

Após o recebimento da uid_pos, (UID da posição) é possível utilizá-la para fazer uma requisição, para isto:

```
curl -X GET  -H "Authorization: Bearer {token}" 'https://api.acessorh.com.br/v1/positions/{uid_pos}'
```

A requisição acima retornará um [JSON](#json) com o seguinte valor da posição:

```go
HTTP/1.1 200 OK
Content-Type: application/json
{
  "_id":("xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"),
  "profile":{
    "name":"xxx",
    "email":"acesso@io.com",
    "mobile":"11 12345 1234"
  },
  "sms_sent":"11 12345 1234",
  "email_sent":"acesso@io.com",
  "jornada":"Descrição da jornada",
  "acc":("xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"),
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
Resultado em [JSON](#json) de todos campos adicionais:

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
          "url": "https://api.acessorh.com.br/v1/files/caminho-do-arquivo.jpg"
        },
        "foto2": {
          "url": "https://api.acessorh.com.br/v1/files/caminho-do-arquivo.jpg"
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
          "url": "https://api.acessorh.com.br/v1/files/caminho-do-arquivo.jpg"
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
          "url": "https://api.acessorh.com.br/v1/files/caminho-do-arquivo.jpg"
        },
        "foto2": {
          "url": "https://api.acessorh.com.br/v1/files/caminho-do-arquivo.jpg"
        },
        "foto3": {
          "url": "https://api.acessorh.com.br/v1/files/caminho-do-arquivo.jpg"
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
          "url": "https://api.acessorh.com.br/v1/files/caminho-do-arquivo.jpg"
        },
        "foto2": {
          "url": "https://api.acessorh.com.br/v1/files/caminho-do-arquivo.jpg"
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
          "url": "https://api.acessorh.com.br/v1/files/caminho-do-arquivo.jpg"
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
          "url": "https://api.acessorh.com.br/v1/files/caminho-do-arquivo.jpg"
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
          "url": "https://api.acessorh.com.br/v1/files/caminho-do-arquivo.jpg"
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
          "url": "https://api.acessorh.com.br/v1/files/caminho-do-arquivo.jpg"
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
          "url": "https://api.acessorh.com.br/v1/files/caminho-do-arquivo.jpg"
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
          "url": "https://api.acessorh.com.br/v1/files/caminho-do-arquivo.jpg"
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
          "url": "https://api.acessorh.com.br/v1/files/caminho-do-arquivo.jpg"
        },
        "foto2": {
          "url": "https://api.acessorh.com.br/v1/files/caminho-do-arquivo.jpg"
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
          "url": "https://api.acessorh.com.br/v1/files/caminho-do-arquivo.jpg"
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
          "url": "https://api.acessorh.com.br/v1/files/caminho-do-arquivo.jpg"
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
          "url": "https://api.acessorh.com.br/v1/files/caminho-do-arquivo.jpg"
        },
        "foto2": {
          "url": "https://api.acessorh.com.br/v1/files/caminho-do-arquivo.jpg"
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
    "email": "acesso@io.com",
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

É possível consultar múltiplas posições relativas a uma conta ou organização (dependendo da permissão do [token](#token)). Para isto enviar a seguinte requisição:

```
curl -X GET -H "Authorization: Bearer {token}" 'https://api.acessorh.com.br/v1/api/v1/positions'
```

Esta requisição responderá um [JSON](#json) com objeto, cabeçalho e uma lista de posições:

```go
HTTP/1.1 200 OK
Content-Type: application/json
{
  "offset":0,
  "count":2,
  "positions":[
    {
      "_id":("xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"),
      "profile":{
        "name":"xxx",
        "email":"acesso@io.com",
        "mobile":"11 12345 1234"
      },
      "sms_sent":"11 12345 1234",
      "email_sent":"acesso@io.com",
      "jornada":"Descrição da jornada",
      "acc":("56786739-8916-7259-6730-815092597835"),
      "status":240,
      "pagamento":{"vinculo":"clt","valor":"R$ 4.321,00"},
      "admission_date":"2018-08-08 00:00:00.000Z",
      "_created":"2017-07-17 19:03:24.216Z",
      "_updated":"2017-07-17 19:03:24.216Z"
    },
    {
      "_id":("xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"),
      "profile":{
        "name":"xyz",
        "email":"acesso@io.com",
        "mobile":"11 54321 4321"
      },
      "sms_sent":"11 54321 4321",
      "email_sent":"acesso@io.com",
      "jornada":"Descrição da jornada",
      "acc":("12345678-1234-1234-1234-123456789123"),
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
      "_id":("xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"),
      "profile":{
        "name":"xyz",
        "email":"acesso@io.com",
        "mobile":"11 54321 4321"
      },
      "sms_sent":"11 54321 4321",
      "email_sent":"acesso@io.com",
      "jornada":"Descrição da jornada",
      "acc":("12345678-1234-1234-1234-123456789123"),
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
     "url": "xxxxxxx"
   },
   "city": "São Paulo",
   "cnpj": "xxxxxxxxx",
   "district": "Cidade Monções",
   "email": "acesso@io.com",
   "id": "2f23fff1-170b-4e7a-a74b-a79aa9dad418",
   "name": "AcessoBIO",
   "org": ("xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"),
   "phone_number": "00 00000-0000",
   "state": "São Paulo",
   "trading_name": "Acesso IO LTDA",
   "zip_code": "0000000"
 }
 {
   "address": "Praça General Gentil Falcão",
   "address2": "000",
   "brand": {
     "url": "xxxxxxx"
   },
   "city": "São Paulo",
   "cnpj": "xxxxxxxxx",
   "district": "Cidade Monções",
   "email": "acesso@io.com",
   "id": "5f23faf1-170b-4f7a-a74b-a79cd9dad348",
   "name": "AcessoRH",
   "org": ("xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"),
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
curl -X GET  -H "Authorization: Bearer {token}" 'https://api.acessorh.com.br/v1/accounts/{uid_acc}'
```

Resultado em [JSON](#json):
```go
HTTP/1.1 200 OK
Content-Type: application/json
{
  "address": "Praça General Gentil Falcão",
  "address2": "000",
  "brand": {
    "url": "xxxxxxx"
  },
  "city": "São Paulo",
  "cnpj": "xxxxxxxxx",
  "district": "Cidade Monções",
  "email": "acesso@io.com",
  "id": "2f23fff1-170b-4e7a-a74b-a79aa9dad418",
  "name": "xyz",
  "org": ("xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"),
  "phone_number": "00 00000-0000",
  "state": "São Paulo",
  "trading_name": "Acesso IO LTDA",
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
