# **Acesso RH - Cadastramento em lote**

 Documentação para requisição de cadastramento de usuários em lote.

###**Modelo de envio**

O cadastramento em lote é feito através do envio de um arquivo .csv com as tags dos campos, separados por colunas.

**Considerações gerais:**
- O arquivo precisa estar no formato UTF-8
- Precisa ser um arquivo CSV válido
- Não importa a ordem dos campos
###**Descrição de cada campo:**
|Tag|Descrição|
|--------|-----|
|empresa|Texto exato, diferenciando caracteres maiusculo de minusculo.
|cargo|Texto exato, diferenciando caracteres maiusculo de minusculo.
|departamento|Texto exato, diferenciando caracteres maiusculo de minusculo.
|data_limite|Formato (YYYY-MM-DD).
|jornada|Texto com no máximo 255 caracteres.
|pagamento_vinculo| Uma das opções: clt, estagio, aprendiz, autonomo, temporario.
|pagamento_recorrencia|Uma das opções: horista, mensalista, aulista, comissionista, diarista.
|pagamento_valor|Decimal separado por ponto ex.: 2000.00
|contato_nome| Texto com no máximo 255 caracteres.
|contato_email|Email válido.
|contato_celular|Número respeitando a mascara: (##) 9####-####
###**Descrição de cada documento adicional:**
|Tag|Documento|
|--------|-----|
|oab|OAB
|crc|CRC
|crf|CRF
|cnv|Carteira Nacional de Vigilante
|crea|CREA
|antecedentes_federal|Antecedentes Federal
|antecedentes_estadual|Antecedentes Estadual
|bombeiro|Certificado de Bombeiro
|qualificacao_civil|Qualificação Civil (CTPS)
|reciclagem|Certificado de Reciclagem
|vspp|Certificado de Vigilante Segurança Pessoal Privado
|ultimo_voto|Certidão de quitação eleitoral
|vacinacao|Comprovante de Vacinação
|vigilante|Certificado de Vigilante
|receita_federal|Cadastro na Receita Federal


**Formatação de linha CSV:**
```
empresa,departamento,cargo,data_limite,jornada,documentos_extra,pagamento_vinculo,pagamento_valor,pagamento_recorrencia,contato_nome,contato_email,contato_celular
```
**Exemplo:**
```
"Acesso Digital","Almoxarifado","Repositor",2017-08-30,"Segunda a sexta","oab|antecedentes_federal|antecedentes_estadual","clt",2000.00,mensalista,"João da Silva","joao.da.silva@host.com","(11) 90000-0000"
```

