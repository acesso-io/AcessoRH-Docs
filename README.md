# AcessoRH API

A API ...(...)

## Índice

##### 1. [**Recebendo notificação de posições concluídas**](#recebendo-notificação-de-posições-concluídas)
##### 2. **Consultando uma posição**
##### 3. **Consultando multiplas requisições**
##### 4. **Token de autenticação**

[click on this link](#my-multi-word-header)


### Recebendo notificação de posições concluídas

O AcessoRH disponibiliza uma plataforma para verificar a **conclusão** de um cadastro.
Para isto basta fornecer uma URL apta a receber uma requisição POST, com isto a API da acessoRH irá enviar a seguinte requisição:

```
POST curl 'http://{{sua URL}}' -d '{org:xxxx-xxxxx-xxxx-xxxx,acc:xxxx-xxxx-xxxx-xxxx,pos:xxxx-xxxx-xxxx-xxxx}'
```

Sempre que uma posição for concluída a API irá procurar as URLs cadastradas relativas a esta posição e enviará a informação por chamada REST.

## My Multi Word Header

### Consultando uma posição

Após o recebimento da UID da posição é possível fazer uma requisição para consultar os dados desta posição, para isto:

```
POST curl -H "Authorization: Bearer {token}" 'http://www.acessorh.com.br/api/v1/position/{uid_pos}'
```

A requisição acima irá retornar on json com o seguinte valor:

```json
{
  "_id":UUID("xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"),
  "profile":{
    "name":"xxx",
    "email":"xxx@yyy.com",
    "mobile":"11 12345 12345"
  },
  "sms_sent":"11 12345 12345",
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

Além destes campos, é possível acrescentar outros campos opcionais:

- docs: A lista de documentos enviados pelo usuário (cpf, rg, endereço)
- role: O cargo que foi contratado
- department: O Departamento

Caso queira acrescentar estes campos adicionais na resposta, basta inclui-los no request, colocando o parametro **include** da seguinte forma:

```
POST curl -H "Authorization: Bearer {token}" 'http://www.acessorh.com.br/api/v1/positions/{uid_pos}?include=docs,exame,role,department'
```

Exemplos:

```
GET api/V1/position/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx?include=docs,exame
GET api/V1/position/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx?include=department,role
```

### Consultando multiplas requisições

É possível consultar multiplas posições relativas a uma conta ou organização (dependendo da permissão do token). Para isto enviar a seguinte requisição:

```
POST curl -H "Authorization: Bearer {token}" 'http://www.acessorh.com.br/api/v1/positions'
```

Esta requisição irá responder um Json com um objeto com um cabeçalho e uma lista de posições:

```json
{
  "count":5,
  "positions"[{...},{...},{...},{...},{...}]
}
```
É possível filtrar a busca por **account**.

```
POST curl -H "Authorization: Bearer {token}" 'http://www.acessorh.com.br/api/v1/positions?acc=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'
```

Nestas buscas não pode ser passado como parametros os **includes**, sendo que estes apenas podem ser exibidos na busca por uma posição da account.

### Token de autenticação

Os tokens de autenticação são fixos e com data de validade de um ano. Eles podem ter dois tipos de roles:

- Conta: Permite usar a API apenas para consulta interna de uma conta, ou seja, apenas às posições relativa àquela conta
- Organização: Permite uma visualização ampla.
