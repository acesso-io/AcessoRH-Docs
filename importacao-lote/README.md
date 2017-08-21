# **Importação em lote**

 Documentação para requisição de cadastramento de funcionários em lote.

### **Modelo de envio**

O cadastramento em lote é feito através do envio de um arquivo `.csv` com as tags dos campos, separados por vírgula.

**Colunas do arquivo CSV:**

```
empresa,departamento,cargo,data_limite,jornada,pagamento_vinculo,pagamento_valor,pagamento_recorrencia,contato_nome,contato_email,contato_celular,documentos_extra
```

**Exemplo:**

```
"Acesso Digital","Almoxarifado","Repositor",2017-08-30,"Segunda a sexta","clt",2000.00,mensalista,"João da Silva","joao.da.silva@host.com","(11) 90000-0000","oab;antecedentes_federal;antecedentes_estadual"
```

**Considerações gerais:**

- O arquivo deve estar no formato UTF-8.
- Precisa ser um arquivo CSV válido.
- Os campos precisam seguir exatamente a ordem, conforme no exemplo.
- Os documentos adicionais devem ser separados por `;` (ponto e vírgula).
- Os campos descritos como **"Texto exato"** devem corresponder exatamente ao cadastrado na plataforma.

### **Descrição de cada campo:**

|Tag|Descrição|
|--------|-----|
|empresa|Noma fantasia - Texto exato, diferenciando caracteres maiúsculos de minúsculos.|
|cargo|Texto exato, diferenciando caracteres maiúsculos de minúsculos.|
|departamento|Texto exato, diferenciando caracteres maiúsculos de minúsculos.|
|data_limite|Formato (YYYY-MM-DD).|
|jornada|Texto com no máximo 255 caracteres.|
|pagamento_vinculo|Uma das opções: clt, estágio, aprendiz, autônomo, temporário.|
|pagamento_recorrencia|Uma das opções: horista, mensalista, aulista, comissionista, diarista.|
|pagamento_valor|Decimal separado por ponto. Exemplo: 2000.00|
|contato_nome|Texto com no máximo 255 caracteres.|
|contato_email|E-mail válido.|
|contato_celular|Número respeitando a máscara: (##) 9####-####|
|documentos_extra|Lista de documentos adicionais separados por `;` (ponto e vírgula), conforme tabela abaixo.|

### **Descrição de cada documento adicional:**

|Tag|Documento|
|--------|-----|
|oab|OAB - Ordem dos Advogados do Brasil|
|crc|CRC - Conselho Regional de Contabilidade|
|crf|CRF - Conselho Regional de Farmácia|
|cnv|Carteira Nacional de Vigilante|
|crea|CREA - Conselho Regional de Engenharia e Arquitetura|
|antecedentes_federal|Atestado de antecedentes criminais - Federal|
|antecedentes_estadual|Atestado de antecedentes criminais - Estadual|
|bombeiro|Certificado de Bombeiro|
|qualificacao_civil|Qualificação Civil (CTPS)|
|reciclagem|Certificado de Reciclagem|
|vspp|Certificado de Vigilante Segurança Pessoal Privado|
|ultimo_voto|Certidão de quitação eleitoral|
|vacinacao|Comprovante de Vacinação|
|vigilante|Certificado de Vigilante|
|receita_federal|Comprovante de situação do cadastro na Receita Federal|
