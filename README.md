# **AcessoRH API**

Plataforma da AcessoRH para consulta de cadastros concluídos.

## Índice

##### 1. [**Recebendo notificação de posições concluídas**](#recebendo-notificação-de-posições-concluídas)
##### 2. [**Consultando uma posição**](#consultando-uma-posição)
##### 3. [**Consultando múltiplas requisições**](#consultando-múltiplas-requisições)
##### 4. [**Token de autenticação**](#token-de-autenticação)


### Recebendo notificação de posições concluídas

O AcessoRH disponibiliza uma plataforma para verificar a **conclusão** de um cadastro.
Para isto basta fornecer uma URL apta a receber uma requisição POST, com isto a API da acessoRH enviará a seguinte requisição:

```
POST curl 'http://{{sua URL}}' -d '{org:xxxx-xxxxx-xxxx-xxxx,acc:xxxx-xxxx-xxxx-xxxx,pos:xxxx-xxxx-xxxx-xxxx}'
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

- docs: A lista de documentos enviados pelo usuário (cpf, rg, endereço)
- role: O cargo que foi contratado
- account: A conta na qual a posição foi adicionada
- department: O Departamento

Caso queira acrescentar estes campos adicionais na resposta, basta inclui-los no request, colocando o parâmetro **include** da seguinte forma:

```
POST curl -H "Authorization: Bearer {token}" 'http://www.acessorh.com.br/api/v1/positions/{uid_pos}?include=docs,exame,role,department'
```

Exemplos:

```
GET api/V1/position/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx?include=docs,exame
```
```
GET api/V1/position/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx?include=department,role
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
