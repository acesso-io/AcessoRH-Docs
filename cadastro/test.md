# **Acesso RH - Cadastramento em lote**

 Documentação para cadastramento de usuários em lote.

**Exemplo de CSV:**
```
empresa,departamento,cargo,data_limite,jornada,documentos_extra,pagamento_vinculo,pagamento_valor,pagamento_recorrencia,contato_nome,contato_email,contato_celular
```
```

"Matriz","Almoxarifado","Repositor",2017-08-30,"Segunda a sexta","oab|antecedentes_federal|antecedentes_estadual","clt",2000.00,mensalista,"João da Silva","joao.da.silva@host.com","(11) 90000-0000"
```


Considerações gerais:
- arquivo precisa estar no formato UTF-8
- precisa ser um arquivo CSV válido
- não importa a ordem das colunas


Descrição de cada campo:
empresa: texto exato, diferencia maiuscula de minuscula
cargo: texto exato, diferencia maiuscula de minuscula
departamento: texto exato, diferencia maiuscula de minuscula
data_limite: formato ANO-MÊS-DIA (YYYY-MM-DD)
jornada: texto, limite de 255 caracteres
documentos_extra: nome dos documentos adicionais separados por virgula (conforme lista em anexo)
pagamento_vinculo: Uma das opções: clt, estagio, aprendiz, autonomo, temporario
pagamento_valor: decimal separado por ponto ex.: 2000.00
pagamento_recorrencia: Uma das opções: horista, mensalista, aulista, comissionista, diarista
contato_nome: texto, limite de 255 caracteres
contato_email: email válido
contato_celular: numero respeitando a mascara: (##) 9####-####


Lista dos documentos adicionais:
oab - OAB
crc - CRC
crf - CRF
cnv - Carteira Nacional de Vigilante
crea - CREA
antecedentes_federal - Antecedentes Federal
antecedentes_estadual - Antecedentes Estadual
Certificado de Vigilante
Certificado de Bombeiro
Qualificação Civil - CTPS
Certificado de Reciclagem
Certificado de VSPP
Certidão de quitação eleitoral
Situação Cadastral no CPF
