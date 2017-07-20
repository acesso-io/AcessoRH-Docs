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
      "url": "https://storage.googleapis.com/acessorh-dev/acc/4be06c05-8fd6-4ef2-a2dd-ac78a4d29953/4573eec6-25b8-41f9-bb49-9a5c3fcb9fff/brand/17b38521-7d6e-46d3-912c-3eff95f5660e?Expires=1500669724&GoogleAccessId=acessorh-public-files%40acesso-io.iam.gserviceaccount.com&Signature=CUUXchCSniIxCuGQ3baxNTdiLj2rRQz1leb%2FMpBoV%2F6BaNUnkjNffrDIpGiM08ePPrk7xuhXwNnRk6vr3PN13dlfg2RWaM4CMFI6VlKvsP8%2B10jQD8fobmFT2mJNO6TpPV6Zfk6jnDVxaSmkB3p%2BnPToIfEyutmlFm6YAlW%2BHldhm7dp88MY0fpfMNJ3fESvwY3xj54OWo7DOcdfGRw0crEjX1ly%2B9iRlLI4klPu4UY5exEvf6enr46Ga8Q1rODRFRkIziMAx5HSAD6ymeqJv2rCedAhbEYH78EGVAIHVXas7pD6whPAKv%2FmMtYsdznE8zjd8zhvSWwJwUbIFDmxtg%3D%3D"
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
          "url": "https://storage.googleapis.com/acessorh-dev/doc/br/5d2c6708-e996-4cc7-ac17-f8c067088cc1/e600194d-5f9d-4d73-bbc8-09b3a21cf81d/5bb67d91-99ef-469c-ae73-f51498621acb/51a357fb-f56e-47f4-85e5-e5ff1bcf0db6?Expires=1500669724&GoogleAccessId=acessorh-public-files%40acesso-io.iam.gserviceaccount.com&Signature=TMxEfLbDqRiKrIajSnLwDMSHGzkiVcHljmRfpUbLovAkUjwKtsrXITsjtJthyyMgRw8MujXLVqBy%2F9MJ%2FujUNnISeSiA61GH1UGXE5j2%2Fooc7G%2B74mFQs3Z8xohm6D7EQ6RQ7SxF%2B4GTGMQxKGrWDVwDPgg2JKz%2FHc9BlQM9yVn3n0mRuqQrk6xfeJwD6iLTHnkly9yfJlGedp8LkWRQsG%2FuA%2FUDTYfw%2BXNO9cPCBrYmk1RRGqcmfwdgmBHO8ScpUqmbTqcS3X%2F%2FO9iVlX7GewPAuMqjZojX%2FXG8D4kdJzwtD4P5TFbOQ2oQf2ViXLfvQ6tXBoq1A2JpEVxiFRqoAg%3D%3D"
        },
        "foto2": {
          "url": "https://storage.googleapis.com/acessorh-dev/doc/br/5d2c6708-e996-4cc7-ac17-f8c067088cc1/e600194d-5f9d-4d73-bbc8-09b3a21cf81d/5bb67d91-99ef-469c-ae73-f51498621acb/883e7357-adb1-4760-94f0-0069ad2c7567?Expires=1500669724&GoogleAccessId=acessorh-public-files%40acesso-io.iam.gserviceaccount.com&Signature=wdv08NqIIQ05b8W4wAsAPBOJzDwDI4Glb2wUaCliNC1jS%2F4xxIO0vXGkiAHbDTEhG4HjpiYX7hKrYoHR06HcgmeF6nO7BC83CNGWsEcv05ObtIbTFG1tioiGJl0zI3QrDDIlQkUamFp6TBkVSKRoyyR8OvntUEjj4PO9mczeZycYgMNzy7z3NhCp75vtKxBYcdfZ%2FSUkKtJLgsphCiPAVqE8psRm%2B6r40MP%2BSaLHUhTRJPuz7h%2BwrjepBhWTczuF8mdp83celCsx9nZ%2Fs%2FPwsTBJflvR8cKupkGlhUUOMMBd73BQqvvdUGymmnNVpodqAn%2FXjukyzr%2BB6aHLyXX8LA%3D%3D"
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
          "url": "https://storage.googleapis.com/acessorh-dev/doc/br/5d2c6708-e996-4cc7-ac17-f8c067088cc1/e600194d-5f9d-4d73-bbc8-09b3a21cf81d/a8a89892-c9fd-40df-8dfd-5fa694c59ddf/83c53605-5f72-4b9b-b5ae-1f49f74409a0?Expires=1500669724&GoogleAccessId=acessorh-public-files%40acesso-io.iam.gserviceaccount.com&Signature=tqxxSsIfexnEjRplc5qMdn3klscKQqv4Ob%2Fi4i%2F3oGZqUh6RU%2FgzyQrHS7Nk%2B2wIuoUnppQwhQmGFxcgbSozjPvQhJgXcrENn8qvYxD8sEQXOH1se7V3j2rMO3xGpqls28bu%2Bo80xos4bXF6N4kL%2FVzLDncwAzOu1LYBrb%2FF15r4CUsxBCbXLNuu%2FWKuT8if5%2FsxvwNlDMK2f87VNj%2BECGUSOnYlky8qUU7Hv2l6jlyRaWJyaHjguLMqkyDiqidfRZtKaWeCOW6RGKTIy9Vcw6fGA3r%2F5Jc5d6L643oCnjjbnqAxZc3k72P2fOegQiEDQlrC6I20fl8PMbcX0af5nQ%3D%3D"
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
          "url": "https://storage.googleapis.com/acessorh-dev/doc/br/5d2c6708-e996-4cc7-ac17-f8c067088cc1/e600194d-5f9d-4d73-bbc8-09b3a21cf81d/0f98ddee-7bb0-4b32-a849-99d297202a81/0f72dddc-a928-481e-9eb5-c6dc1fc1b70f?Expires=1500669724&GoogleAccessId=acessorh-public-files%40acesso-io.iam.gserviceaccount.com&Signature=UhTJYP%2FdnL22aOjDwHS1%2F9pJRiNiHzrqtBEh20a3A3ATRqJMOQ25B6gQEx5A4JfANdGeJEDGWQqj3kFEa6q4hlPnuMrfM0cK6mx120DaJU5KBVk0S%2Fq5EcULCkouXyEDgbo76dAtE2Abl9bH6juVElIC9K5w7M2k5AmrP5WOc40wIAg%2FLhqpeORSS%2F6%2BG1Q7nJFtgasoHqzGxN52N3nLCEE5iDfAVBNY8XOGL5Fe23tHmIbSk7AIf5GLQc8kf2Z%2Bp0hfZC59VJDSnec14wDEGIY%2BfSa0hdPegsg%2Fiad5T%2BcvvhZoXPSd%2FQiPuWGo2UXNSeEm5Uw8Iv6tbXBwcJjFDg%3D%3D"
        },
        "foto2": {
          "url": "https://storage.googleapis.com/acessorh-dev/doc/br/5d2c6708-e996-4cc7-ac17-f8c067088cc1/e600194d-5f9d-4d73-bbc8-09b3a21cf81d/0f98ddee-7bb0-4b32-a849-99d297202a81/417052bd-a7b2-48e1-8a44-bcdf2d9c1364?Expires=1500669724&GoogleAccessId=acessorh-public-files%40acesso-io.iam.gserviceaccount.com&Signature=MGP40m4hO%2BTIHPQCnBsCgl5Ql%2BodcWdWckyOsIXzxgoEU0QI%2BtHtvvisoZLhJicsFMiChE4CvyfujSL7iX6h2OdGEAYoljn1ftwgC20ZxUHS1ItpPcG8ueqg6EM8%2F0rEXamAQm%2Bc22%2Bngt0yQFiN4rrx%2FshVnHAWhT2FCLCodoiPNShvIfQO4hBkTn7i5X9Skn0UXE9VAmyZpJxLb1mgrt5IYebqYGFz7832wF20lvoWCE9P4ehLWaHGQDrDeJq5uYUpE3pvkVgxg52e%2F9MaZ3GeJcF16j%2BTGT%2Fqf01jE661E16uwp6baGZ2JLz3ZHCiTDB0b73WNF3eCI6sO0G%2F4A%3D%3D"
        },
        "foto3": {
          "url": "https://storage.googleapis.com/acessorh-dev/doc/br/5d2c6708-e996-4cc7-ac17-f8c067088cc1/e600194d-5f9d-4d73-bbc8-09b3a21cf81d/0f98ddee-7bb0-4b32-a849-99d297202a81/a7e4dffd-620a-4b77-bb3b-87ab32d3bc32?Expires=1500669724&GoogleAccessId=acessorh-public-files%40acesso-io.iam.gserviceaccount.com&Signature=e291tdcL2vanAcgG4uenatKstvX%2BYBm6pjSOtWwmR6Dwy3gUHMV7wNhiAVwPxRK9htCzDufstMDuuVYKBKsimxE%2F50iFdATsrSK2xnGY9uAWK7Olw34sVdmRjoSgbLRpPCzq%2BjOyr%2BKOyzNYTKNl2xg5%2Bl2f3FkBfTqCD%2F177uNEibQ4ockrTJ8ojkL63D1LL2mfKLaPNjvxfkl1XdWbGQlF%2B6CFI5qcYKfBtga04lb6U%2BknJlq273hLau8Ewz08dPYUDGDqn4q9FQT89SynuH8lEPdJvCOcNsjaDfgrVaNd8%2FmjRoukswz3B5Lvp17Ie984MwIZBjG4UhXaNzkIqA%3D%3D"
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
          "url": "https://storage.googleapis.com/acessorh-dev/doc/br/5d2c6708-e996-4cc7-ac17-f8c067088cc1/e600194d-5f9d-4d73-bbc8-09b3a21cf81d/b508549a-c45a-425f-8773-d60dc7484750/d6028ac2-3ad1-44a7-a52f-f283276002f9?Expires=1500669724&GoogleAccessId=acessorh-public-files%40acesso-io.iam.gserviceaccount.com&Signature=hllXpra4yIB0kHb27FrgzVsp1xE1EgkOy4r8nEoOmL19SylqQkPhuBV41A3ZSSjZLisSSFbKjyNX2f2MLnmcxc81ovH38%2F%2FqrCk2iMucwYx%2FoA2om1vsdBCH1a88hbQnLBN0EKoKEhc3qZzlqhCRTNdEh%2FvmMTn1%2F8a5i1Km3fK6qCSzd2g76Kby6kJvi66ktyW16vRi8BPChlGWOh42COQhMCikRz60GRirP%2BZswv78pVSUymux9Ey5ByKrQoQVB0QXkoh3bsXiCnKghUV8W1yofVsT1%2Fwz8CDuyM1ihv3eDXk0XjIxGmKwniflKSTOJpQ57VkN584sjnpyTkoLrQ%3D%3D"
        },
        "foto2": {
          "url": "https://storage.googleapis.com/acessorh-dev/doc/br/5d2c6708-e996-4cc7-ac17-f8c067088cc1/e600194d-5f9d-4d73-bbc8-09b3a21cf81d/b508549a-c45a-425f-8773-d60dc7484750/4d00bf1b-e010-455e-a00f-4ac625f2fb7e?Expires=1500669724&GoogleAccessId=acessorh-public-files%40acesso-io.iam.gserviceaccount.com&Signature=dumHf1J5QcjjmKvkpkw34hodR5p5h0jplzTWhah1KQO9Gv29TLtWiaFLLOuTvxYnymA8BFgo7l0QvnZI4DeDu46RMDZNMZPn7Zaw13j88BurGc%2BqSGF78cQQNB8TLzaiauILkncVkErjS1WGFGVpdE31mGtwRdFC4Us3nLeGsmhDGwje842%2Fhs0Scq639sdltuk9Qsej4xa0RlWEFefrueMgcAMtZUvaaHFKHEZT0jPl6dFpzpb1%2FY4HQibks4O1n8c8jFIivRV6SBjC1Y4I2ifvO5DXV6sM7mA8KhXCIu3CHExSlmQbLFlpj8yapgLpDTvOvxZ8FyaRD5jR4G3EiQ%3D%3D"
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
          "url": "https://storage.googleapis.com/acessorh-dev/doc/br/5d2c6708-e996-4cc7-ac17-f8c067088cc1/e600194d-5f9d-4d73-bbc8-09b3a21cf81d/6d58e6f3-5501-40b3-b1fb-3e6949d19960/1145a3bc-60d2-4654-8a56-737029ff5c0f?Expires=1500669724&GoogleAccessId=acessorh-public-files%40acesso-io.iam.gserviceaccount.com&Signature=j6fUq2m6piDRVEqcUqE4PeTOXspCvprMXy9X5TqIyTJLVIc9IcuNOQg0a0%2Ff4N3TEfecVjsGt5rvIjpsBDRmVFAoFplRp%2F5yYBYs48cFgx4fR%2F%2BWJfSU%2Bz2HdVOUNiqbJ6rhkV0B40WOUkB1XNjZbkcGkN%2BkEaQmTDNLIxJc5P5UPtbgxhzt9XPGWHc3GMRBi3JQxqHrB3uPRf24FgwGP%2FDVL8DdkJDJ%2BHIapma7J5iiO6a3d6p7uWoSZV3b9imQzYNa9%2F8Bg5mXsH8hzkO%2ByWkNHv0b%2BsmML3WsKaKzmu8I3QRABxGhXyBsNE377qwjpvIkOFRDLUaK5FQdy1CLdQ%3D%3D"
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
          "url": "https://storage.googleapis.com/acessorh-dev/doc/br/5d2c6708-e996-4cc7-ac17-f8c067088cc1/e600194d-5f9d-4d73-bbc8-09b3a21cf81d/7d5eb9da-b50e-49f7-8c3e-3ee5877eb620/b17eb1bc-ad39-4169-aea5-9273193e69b5?Expires=1500669724&GoogleAccessId=acessorh-public-files%40acesso-io.iam.gserviceaccount.com&Signature=QEKHYiMBH2DJF4dhwGG%2FmgF9KLbSdjsr6hKNY%2BJZrQs6DWM4i8HOIvpQgXGE%2BRK9NeQBfog3aTf%2FXrtaKdgA%2BGKhWsHGwIek4qHeKX%2BlH5NgeiVBMFI2qUwPNOdgWOGoi1ym0cyaeZ5S9LCAP5MY39K8uivnV2LuWheEmmQDIShThKSvS5YlGxEHaTt1IXCz7CqsqLaiyA8QwoyPcnHYK3vct2B1xJF3j8oFzDlbyYCMBvz%2BHa9lnau9TCcBWobKflSgNZb554twvyZWyhuM%2FBYYZl6xRQtbvAX3MoaTUOZ8NvSIzb3bsOLGmhBXsOTFrKpqoS4wBw3CdfOyywgByA%3D%3D"
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
          "url": "https://storage.googleapis.com/acessorh-dev/doc/br/5d2c6708-e996-4cc7-ac17-f8c067088cc1/e600194d-5f9d-4d73-bbc8-09b3a21cf81d/93c3527c-1bed-45d0-8725-1705e580357d/40408dfd-552d-47fb-928a-9cb31d3d842d?Expires=1500669724&GoogleAccessId=acessorh-public-files%40acesso-io.iam.gserviceaccount.com&Signature=KtrT7Qu1u3B%2B3XWTCt7WS5gqw6cCvQgE9r5rF3IjlRipVq%2F4ZopylZmCkX3j0mlHqwzrGT6UOcietFrj3ek2Q3Gr5pZN%2BiC4ySvKaVw0iv8cRuXOYdE0pSsbuEqVx3QTl7RJjEHIiQPs8UHXoy%2Baoq3eGOV5%2FJUQIm9pLOCwmEUAO5hZBJZoBa9OyNOk%2BmghmfNYeq57treGQV%2FvsoDQqS1jfFP5JvWdPKxsUTfzmcNOv7S4VRtz9AmCal03l7n%2FILubHY%2FvRs%2B%2Bg628CfUyTDqh6%2FW5cPi%2B2Emm91iKKsTAc9gdAfI7UGKWwbaA%2FkqJE%2BLb%2BMwLhUHCxjGANSgPuw%3D%3D"
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
          "url": "https://storage.googleapis.com/acessorh-dev/doc/br/5d2c6708-e996-4cc7-ac17-f8c067088cc1/e600194d-5f9d-4d73-bbc8-09b3a21cf81d/35e0ebd9-c248-4dff-b671-54ffa8a46501/97861289-d145-4e02-a756-3e17e670644c?Expires=1500669724&GoogleAccessId=acessorh-public-files%40acesso-io.iam.gserviceaccount.com&Signature=v0QsduWzoY4hb2gCEgre6lJUtlfCLMcED05P9VbYYnv3R6dIkJLsKFPgt903Q7m03UNjpjWU6X5SRXcJUhEL0vHzZWjU5A%2BR%2B2OVbempTJKuXFYeG51JTAEYgpnrnef2z8Gg5Udc8EEjp4e5Al4hNQ2PNy8%2FZ9dzUzTVm5Ez9TnilGBjiKzwlVywbrIIqccnHU8Nlt%2BJFMLl3PlLmgw35yVh2LvxYsv352G9caAVTvfVrdFJjWQXzJgASWXv3sEiViyXoJ%2B1zCidKPswZYb8nhTxq1gRAgpfmmW0Pu%2Fc%2Fo1Tzomagooqq6Qx1ajK98c6JMIt8%2Ft7iXiUOwYD69W2Nw%3D%3D"
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
          "url": "https://storage.googleapis.com/acessorh-dev/doc/br/5d2c6708-e996-4cc7-ac17-f8c067088cc1/e600194d-5f9d-4d73-bbc8-09b3a21cf81d/ce5fda44-fbad-4dbd-84de-2c30d110e71e/e1c0dfa7-48da-446a-8200-0ece23d4abbe?Expires=1500669724&GoogleAccessId=acessorh-public-files%40acesso-io.iam.gserviceaccount.com&Signature=Pc82DOFHZ88YP7pYtrYBbXYNcMUT04lN%2BAHB%2FHmwCVEFf3wXXt0Zfs7yG9YTZg6aX9xLdWlp1iYcvfQNojoNmzdaxE9pv341zF5jw8kPqnalRB3q8mtiRr2AnH%2Blpkx34anT2ERedq5uSdygP3Rf3Scsze0Pl86vHKSPwrvRelaSsGmJUApVm61z710ec4ON79uUgo6PfCbWicArsjmNf%2FxaX3ozQ9kLVtua2Tffr%2Bf3qzkZ8DZw1R%2FOpmnaJ88%2B%2FOM7mZ%2B1VfaDwC316PQfTmk6eTvNXgbDL6SRNbgGcBU0QF%2F0rh6e%2F5aC1qAdY1JL88VHqJIonG2BzLgyZ9M33Q%3D%3D"
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
          "url": "https://storage.googleapis.com/acessorh-dev/doc/br/5d2c6708-e996-4cc7-ac17-f8c067088cc1/e600194d-5f9d-4d73-bbc8-09b3a21cf81d/4c92e63b-5e92-4499-979f-4f3e233d596e/94103cb5-f470-41bc-bd48-de8c2f2c5c42?Expires=1500669724&GoogleAccessId=acessorh-public-files%40acesso-io.iam.gserviceaccount.com&Signature=L4v1WgO%2BGxNpxHIChGykxzQqgUmgUultKKKC%2FKu5q9DtRCzyQ50iIUaENrHMnMMqMUEf6ylLryo8jRIPkKY5%2FGX5ucvA3Bdc2MxOp2C3RiaZ028EY0B9oBpksky1PPkQZGFPWpL78dftQtBu%2BZtg%2BiU97SoWUTSg2JYwEUem1cgsqJ48LF9EvPXRuGiT%2BmJVOzp6F%2BwuSKTMWNtvWjVsWrd9Xg%2BKpB39PIGquLlIzqcoD2KFm7x7dEgaxnL7bLiCMhNGPixmZx4YT3c54T0%2F%2BHtv%2FysZdwjOM5Q7rEsoQ8TGSwV7bsVLpsewLFdI0Mu6%2B7Kjv%2BPI4VgOtVutIJVb0A%3D%3D"
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
          "url": "https://storage.googleapis.com/acessorh-dev/doc/br/5d2c6708-e996-4cc7-ac17-f8c067088cc1/e600194d-5f9d-4d73-bbc8-09b3a21cf81d/c2069ecf-ea5d-4029-9960-6f802392c6d7/28450166-fe14-43f2-9793-852689f54e26?Expires=1500669724&GoogleAccessId=acessorh-public-files%40acesso-io.iam.gserviceaccount.com&Signature=hdUfs5xC5GqJIf%2Bbq8v1sFYcrrD6BJ5WXaGx%2Bx811abuGvrX8CdUFUrqWR3u78uXTHyeSOui5%2FYze2bA3MVS2tHSapGg8O170m1VaallQxn2IEdb8pJ4Xe8NyHPENRGR0k2%2FJh1QT%2Bj0bDFsq0KaPl06VDFZpMAwqdEnVJG%2BrsVewKgjkThzxT%2Fr3oJyXmeCPJo9yAStGal2ZYKGjYhg593SM0soU3sYc6IQd1qteWZduL6gd7TOVkFHUTwvA7LFfnhBPlJUdaWUM0c8mB2XdlHlwQj72SWNsHh68q1D5YhcL1fQ4w5vlUCE3%2B6Hxds1Z0oP%2BeU%2BJNGDbjn75j%2BENA%3D%3D"
        },
        "foto2": {
          "url": "https://storage.googleapis.com/acessorh-dev/doc/br/5d2c6708-e996-4cc7-ac17-f8c067088cc1/e600194d-5f9d-4d73-bbc8-09b3a21cf81d/c2069ecf-ea5d-4029-9960-6f802392c6d7/ddc306f9-9241-498c-b22b-62b883b833f5?Expires=1500669724&GoogleAccessId=acessorh-public-files%40acesso-io.iam.gserviceaccount.com&Signature=n0hc1EV7lT0oeh4XxuBXn9CNLRC0LOgs%2FKHYNULLn51PdJvxoZXyeEKeLmSNifwrORFOP6uD8%2BXP130g98KcMk%2F8J7nQvKqfXRanNISQ5LLjhwRzuC0QOMcw%2F8%2BhCEj1Ehcj78ltlXM0Vdbypo7keZVcXnVKSrbsi146b2Yu5ogg0mctoe41GlDPi%2FxWgUsqfoZng8mEqDxao03mzarzmy8dEF18sJPl7TxvwhBrkDR5jCTEJTaM7M9lTTAJ7v77xRiv3HaJyoEPoIU%2F5qL%2Bry5LOEAFNrk7%2FK%2BT2xx5Nfa%2FNY9nQNm%2F71P9J7KBnfnQADM0HT1TTUZPXnhlZoR1SA%3D%3D"
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
          "url": "https://storage.googleapis.com/acessorh-dev/doc/br/5d2c6708-e996-4cc7-ac17-f8c067088cc1/e600194d-5f9d-4d73-bbc8-09b3a21cf81d/e2a41d40-f60b-48fa-80c5-87812e165fbe/a7c3edc8-6962-40c1-93ad-06b358e61fb0?Expires=1500669724&GoogleAccessId=acessorh-public-files%40acesso-io.iam.gserviceaccount.com&Signature=x4eZ8%2B2lEAYCfPWt%2Bml9Nb5%2BB2xuDLUaDoiq%2BkMilMKWVElQprTlKHqGXlR%2Fk%2FnqirZiaB8t6WX1nWAWTwYxLZXkHznuYvp%2BlnubEQDViE28RQc3QI1fbs9ppAhCe95u736CDH%2B05Y8tk2JWiCTSy84cyShqPO54i9Pu42j09g8fSESZHqNIDyiK%2Fge4BCsxpoo2e1m5H7MJXR9ha1dG4sDe0SFO5KvOfGaspW6C0sq0VHpktkNfc3O6UenmDU%2FF6BpSogukjxyyTzrNeK3VZIuwvPGzpFPGk6bBPECyj68YkQKA3Pr5XESmdCFLHZZgw5yoKZQ6hiOde8V0SNDhVA%3D%3D"
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
          "url": "https://storage.googleapis.com/acessorh-dev/doc/br/5d2c6708-e996-4cc7-ac17-f8c067088cc1/e600194d-5f9d-4d73-bbc8-09b3a21cf81d/32e00f6d-e847-4ac8-be6e-13b8c36b7a27/fdbcb021-15d0-4f83-8a13-b9d98bef9604?Expires=1500669724&GoogleAccessId=acessorh-public-files%40acesso-io.iam.gserviceaccount.com&Signature=O5ZbUl0AgiNoOktPpjihLdCK92B3QWNTJpxo88ayh5hag8Xu0ytpjPAAwkNRh6fepXOWR54sJa6qILqnWxe9enV74bBh%2B%2FcmkJHXD%2B2Z9YyeE2qsC5qrUuzcisY76W9k8t99JRYwtHnr2ehNySK9yhJfxNXRbfBASO2PLEcFlEdXcl31FR03upLM1kYivuSFWsUiPCK6mAsZgzfVCEISv1lLEjr6HaCT1stqmwtozbHxO1gGkoA9SmtHS7TQrYemZiiOkWHHaKBtQxpv5%2Fd%2BGHkNo0fEBe1D9AY6sj%2FYtdumRxlhQ3jwR1soe4h67k2GhxglKEtS%2FMFyycW%2FnzrZuw%3D%3D"
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
          "url": "https://storage.googleapis.com/acessorh-dev/doc/br/5d2c6708-e996-4cc7-ac17-f8c067088cc1/e600194d-5f9d-4d73-bbc8-09b3a21cf81d/7923a0f1-170a-417b-a2c7-bdad95ee4fdc/6f362c6c-9eb6-4495-b17c-f1585d6aa693?Expires=1500669724&GoogleAccessId=acessorh-public-files%40acesso-io.iam.gserviceaccount.com&Signature=n%2BRhXvbc%2BeQD%2FMiLLkrBz9Yc4QivuKQg0UhDHH48AAdL%2BTkP%2BvflffGovBznmbeji%2Fio5FLXlm0JOn9mN2uJyqri2s5ZvXBD6edY9YE97E7HuKHS11mwrOUN8945s2jNMGiBpH8%2F09e8WOYENAQbc%2FuG0fUax0G7Dln4NAZpVwRvpVOrn9XAgSdMlLRp%2BRCguSfXEai77CghDxrTyu8MHlWk8PM2oP78bdqaHG%2BmyBhiydiKmrOgCd3p%2F7ol15m%2BPbqaAFn%2BKGQy52cq9kKtSrHF%2FQ2QCMZwjkDE7vb8RnHyK4jTxdmxzGc7gET3dPveuufccRRv9SUiVgRiiebNCQ%3D%3D"
        },
        "foto2": {
          "url": "https://storage.googleapis.com/acessorh-dev/doc/br/5d2c6708-e996-4cc7-ac17-f8c067088cc1/e600194d-5f9d-4d73-bbc8-09b3a21cf81d/7923a0f1-170a-417b-a2c7-bdad95ee4fdc/dd48a4c6-accc-4064-aa4e-6f0ccb13fd29?Expires=1500669724&GoogleAccessId=acessorh-public-files%40acesso-io.iam.gserviceaccount.com&Signature=ogXjWJyv6ZLzlrK6LWtWVBFPQUfPsT%2BAlQ9QF7TrKNx4pMLVXY%2BDPGoNWr6t0hRXqL3Ai%2F8ZJtcEcnqOvtMyeELKXCANCYUT5IVTVGdL2jG3QVbeiX3LuyaSYJvvptC%2BOovQ32rYxSuxt3OJRvneLFDop0j8kgr%2FO8vX6nwmZyNcXyp1tQbqBrnRYWtJCmyhTfJIGDi4iFM%2FFqLBB0LYKKViVIg3z3xfsPO%2FRTokNoEG8VxupbHgpGzTvQXNt266ibPIKqGtXlsm1UhG8MrCFpPvdlSZ553aLUzP7I3ZX24LGlTvGt5rEB5yt7Hu2xRZOg06Bk5Ogq%2FBuqBZY2cFbA%3D%3D"
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
