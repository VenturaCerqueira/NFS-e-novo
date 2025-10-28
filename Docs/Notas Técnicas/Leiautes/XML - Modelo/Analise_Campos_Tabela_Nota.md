# Análise dos Campos Necessários para a Tabela "nota" Baseado nos XMLs NFSe

## Introdução

Esta análise compara a estrutura atual da tabela "nota" com os campos presentes nos arquivos XML de exemplo `NFSe_sample1.xml` e `NFSe_sample2.xml`, utilizando a tabela detalhada de campos do layout NFS-e fornecida. O objetivo é identificar quais campos já estão mapeados na tabela e sugerir novos campos que precisam ser adicionados para suportar completamente o layout da NFS-e (Nota Fiscal de Serviços Eletrônica) do Sistema Nacional NFS-e.

A tabela "nota" atual possui aproximadamente 58 colunas, cobrindo aspectos como identificação, valores tributários, datas, status e referências a outras entidades (contribuintes, tomadores, cidades, etc.). No entanto, o layout NFS-e é muito mais abrangente, incluindo informações sobre versões, ambientes, tipos de emissão, localidades de incidência, tributação detalhada, benefícios municipais, IBS/CBS (Imposto sobre Bens e Serviços / Contribuição para o Financiamento da Seguridade Social), substituição, detalhes completos de emitente, tomador e intermediário, serviço detalhado, obra, informações complementares, tributos federais detalhados, e muito mais.

**Conclusão Geral**: A tabela "nota" atual NÃO atende todos os campos do layout NFS-e. Embora cubra muitos campos essenciais relacionados a valores tributários e RPS, faltam numerosos campos obrigatórios e opcionais, especialmente aqueles relacionados à DPS (Declaração de Prestação de Serviços), IBS/CBS, detalhes de entidades (emitente, tomador, intermediário), serviço detalhado, e tributação avançada. Recomenda-se adicionar os campos faltantes para conformidade completa com o layout nacional, utilizando as tabelas auxiliares existentes onde apropriado.

## Campos Atuais Mapeados

A tabela "nota" cobre parcialmente os campos do XML. Abaixo, uma lista dos principais mapeamentos identificados (baseados na tabela de campos fornecida):

### NFS-e (Raiz e infNFSe)
- **numero**: Corresponde a `nNFSe` (Número da NFS-e).
- **tipo**: Pode mapear para `tpEmit` (Tipo de emitente) ou `cStat` (Código de status).
- **emissao**: Corresponde a `dhEmi` (Data/Hora de emissão da DPS).
- **competencia**: Corresponde a `dCompet` (Data de competência).
- **status**: Corresponde a `cStat` (Código de status).
- **codigo_verificacao**: Pode ser derivado do `id` da NFS-e ou de campos de validação.
- **info**: Pode armazenar `xOutInf` (Informações adicionais do ISSQN) ou outras informações.
- **numero_processo**: Corresponde a `nProcesso` (Número do processo de suspensão da exigibilidade).

### Valores (ISSQN)
- **valor_servico**: Corresponde a `vServ` (Valor do serviço).
- **valor_liquido**: Corresponde a `vLiq` (Valor líquido).
- **base_calculo**: Corresponde a `vBC` (Base de cálculo do ISSQN).
- **valor_deducoes**: Corresponde a `vDR` ou `vCalcDR` (Valor de deduções/reduções).
- **valor_pis**: Corresponde a `vPis` (Valor do PIS).
- **valor_cofins**: Corresponde a `vCofins` (Valor do COFINS).
- **valor_inss**: Pode mapear para `vRetCP` (Valor da Contribuição Previdenciária).
- **valor_irrf**: Corresponde a `vRetIRRF` (Valor retido de IRRF).
- **valor_csll**: Corresponde a `vRetCSLL` (Valor retido de CSLL).
- **outras_retencoes**: Pode agrupar retenções adicionais.
- **valor_iss**: Corresponde a `vISSQN` (Valor do ISSQN).
- **aliquota**: Corresponde a `pAliqAplic` (Alíquota aplicada).
- **desconto_incondicionado**: Corresponde a `vDescIncond` (Desconto incondicionado).
- **desconto_condicionado**: Corresponde a `vDescCond` (Desconto condicionado).
- **iss_retido**: Pode ser derivado de `tpRetISSQN` (Tipo de retenção do ISSQN).
- **responsavel_retencao**: Pode mapear para `tpRetISSQN` (Tipo de retenção do ISSQN).
- **exigibilidade_iss**: Pode mapear para `tribISSQN` (Tributação do ISSQN).

### DPS (Declaração de Prestação de Serviços)
- **rps_tipo**: Corresponde a `tpEmit` (Emitente da DPS).
- **rps_numero**: Corresponde a `nDPS` (Número da DPS).
- **rps_serie**: Corresponde a `serie` (Série da DPS).
- **rps_emissao**: Corresponde a `dhEmi` (Data/Hora de emissão da DPS).
- **rps_status**: Pode mapear para status da DPS.
- **rps_substituido_tipo, rps_substituido_numero, rps_substituido_serie**: Correspondem a campos de substituição, como `chSubstda` (Chave da nota substituída) e `cMotivo` (Código de motivo).
- **codigo_tributacao_municipio**: Corresponde a `cTribMun` (Código de tributação municipal).
- **discriminacao_servicos**: Corresponde a `xDescServ` (Descrição do serviço).
- **codigo_obra**: Corresponde a `cObra` (Código da obra).
- **numero_artigo**: Pode ser derivado de `idDocTec` (Identificação do documento técnico).
- **evento_identificacao, evento_descricao, evento_info_complementar**: Correspondem a campos de eventos, como `idAtvEv` (Identificação da atividade de evento) e `xNome` (Nome do evento).

### Outros
- **cpf_cnpj_intermediario, razao_social_intermediario**: Correspondem a campos do intermediário (`CNPJ/CPF`, `xNome`).
- **data_cancelamento, motivo_cancelamento**: Campos para cancelamento.
- **fk_nota_contribuinte, fk_nota_tomador, fk_cidade, etc.**: Referências a entidades relacionadas (emitente, tomador, cidades).

Esses mapeamentos cobrem cerca de 40-50% dos campos essenciais, mas muitos campos críticos estão faltando, especialmente os relacionados a versões, ambientes, localidades, tributação detalhada, IBS/CBS, detalhes de entidades e serviço.

## Tabelas Auxiliares Existentes

O sistema já possui tabelas auxiliares relacionadas à tabela "nota" para armazenar informações detalhadas. Abaixo, uma lista dessas tabelas com suas principais colunas e propósitos:

### nota_cancelar
- **Propósito**: Armazenar informações de cancelamento de notas fiscais.
- **Principais Campos**: id, fk_nota, fk_contribuinte, created_at, updated_at.

### nota_contribuinte
- **Propósito**: Detalhes do contribuinte (emitente) associados à nota.
- **Principais Campos**: id, fk_contribuinte_original, fk_pais, fk_cidade, fk_item_servico, razao_social, fantasia, cpf_cnpj, email, contato, contato2, cep, endereco, numero, bairro, complemento, ie, im, aliquota, simples_nacional, regime_especial_tributacao, incentivo_fiscal, porte, isencao, site, imagem, logo, created_at, updated_at.

### nota_deducao
- **Propósito**: Detalhes de deduções aplicadas à nota fiscal.
- **Principais Campos**: id, fk_nota, tipo, descricao, fornecedor_tipo, fornecedor_documento, fornecedor_codigo_pais, data_emissao, valor_deducao, valor_utilizado, documento_tipo, documento_numero, documento_chave, documento_uf, documento_codigo_municipio, created_at, updated_at.

### nota_fila_api
- **Propósito**: Fila para processamento de APIs relacionadas às notas.
- **Principais Campos**: id, fk_contribuinte, protocolo, xml, status, obs, created_at, updated_at.

### nota_tomador
- **Propósito**: Detalhes do tomador (destinatário) associados à nota.
- **Principais Campos**: id, fk_tomador_original, fk_pais, fk_cidade, tipo, razao_social, fantasia, cpf_cnpj, email, contato, contato2, cep, endereco, numero, bairro, complemento, ie, im, created_at, updated_at.

Essas tabelas auxiliares já cobrem muitos dos campos sugeridos para entidades como tomador, contribuinte e deduções, permitindo uma estrutura mais organizada e evitando sobrecarga na tabela principal "nota".

## Campos Faltantes e Sugestões de Novos Campos



### 1. Informações Gerais da NFS-e

| Campo | Tipo | TAM | Descrição |
|-------|------|-----|-----------|
| ID_NFSE| varchar | 53 | Informar o identificador precedido do literal ‘ID’.|
| versao_leiaute | varchar | 1-4V2 | Versão do leiaute da NFS-e (ex.: "1.00") |
| versao_aplicacao | varchar | 1-20 | Versão da aplicação que gerou a NFS-e |
| ambiente_gerador | tinyint | 1 | Ambiente gerador (1 - Sistema Próprio do Município; 2 - Sefin Nacional NFS-e) |
| tipo_emissao | tinyint | 1 | Tipo de emissão (1 - Emissão direta; 2 - Transcrição) |
| processo_emissao | tinyint | 1 | Processo de emissão (1 - App contribuinte; 2 - Web fisco; 3 - App fisco) |
| data_processamento | datetime | - | Data/Hora da validação e geração (formato data/hora) |
| numero_dfe | bigint | 1-13 | Número sequencial do documento |

### 2. Localidades e Tributação

| Campo | Tipo | TAM | Descrição |
|-------|------|-----|-----------|
| codigo_localidade_incidencia | varchar | 7 | Código da localidade de incidência do ISSQN |
| descricao_localidade_incidencia | varchar | 150 | Descrição da localidade de incidência do ISSQN |
| descricao_tributacao_nacional | varchar | 600 | Descrição de tributação nacional |
| descricao_tributacao_municipal | varchar | 600 | Descrição de tributação municipal |
| descricao_nbs | varchar | 600 | Descrição de NBS |

### 3. Benefícios Municipais e ISSQN Detalhado

| Campo | Tipo | TAM | Descrição |
|-------|------|-----|-----------|
| valor_calculo_dr | decimal(15,2) | 1-15V2 | Valor de cálculo de deduções/reduções |
| tipo_beneficio_municipal | varchar | 40 | Tipo de benefício municipal |
| valor_calculo_bm | decimal(15,2) | 1-15V2 | Valor de cálculo de benefício municipal |
| informacoes_adicionais_iss | text | 2000 | Informações adicionais do ISSQN |

### 4. IBS/CBS (Imposto sobre Bens e Serviços / Contribuição para o Financiamento da Seguridade Social)

| Campo | Tipo | TAM | Descrição |
|-------|------|-----|-----------|
| codigo_localidade_incidencia_ibs | varchar | 7 | Código da localidade de incidência para IBS/CBS |
| descricao_localidade_incidencia_ibs | varchar | 150 | Descrição da localidade de incidência para IBS/CBS |
| indicador_operacao_ibs | varchar | 100 | Indicador de operação para IBS/CBS |
| cst_ibs | varchar | 10 | CST para IBS/CBS |
| classificacao_tributaria_ibs | varchar | 100 | Classificação tributária para IBS/CBS |
| percentual_redutor_ibs | decimal(5,2) | 1-5V2 | Percentual redutor para IBS/CBS |
| base_calculo_ibs | decimal(15,2) | 1-15V2 | Base de cálculo para IBS/CBS |
| percentual_ibs_uf | decimal(5,2) | 1-5V2 | Percentual IBS UF |
| aliquota_efetiva_ibs_uf | decimal(5,2) | 1-5V2 | Alíquota efetiva IBS UF |
| valor_total_nf_ibs | decimal(15,2) | 1-15V2 | Valor total NF para IBS |
| valor_total_ibs | decimal(15,2) | 1-15V2 | Valor total IBS |
| valor_total_cbs | decimal(15,2) | 1-15V2 | Valor total CBS |

### 5. Substituição

| Campo | Tipo | TAM | Descrição |
|-------|------|-----|-----------|
| chave_nota_substituida | varchar | 50 | Chave da nota substituída |
| motivo_substituicao | tinyint | 1 | Motivo da substituição |

### 6. Tomador

**Nota**: Os detalhes do tomador já são armazenados na tabela auxiliar `nota_tomador`. Considere adicionar campos adicionais à tabela `nota_tomador` se necessário, em vez de adicionar à tabela principal "nota".

### 7. Serviço Detalhado

| Campo | Tipo | TAM | Descrição |
|-------|------|-----|-----------|
| codigo_tributacao_nacional_servico | varchar | 10 | Código de tributação nacional do serviço |
| codigo_tributacao_municipal_servico | varchar | 10 | Código de tributação municipal do serviço |
| codigo_nbs_servico | varchar | 20 | Código NBS do serviço |
| codigo_contribuinte_intermediario | varchar | 20 | Código do contribuinte intermediário |
| modalidade_prestacao_comex | tinyint | 1 | Modalidade de prestação no comércio exterior |
| vinculo_prestacao_comex | tinyint | 1 | Vínculo de prestação no comércio exterior |
| tipo_moeda_comex | varchar | 10 | Tipo de moeda no comércio exterior |
| valor_servico_moeda_comex | decimal(15,2) | 1-15V2 | Valor do serviço em moeda estrangeira |
| codigo_obra_detalhado | varchar | 30 | Código detalhado da obra |
| codigo_cib_obra | varchar | 20 | Código CIB da obra |
| endereco_obra | text | 500 | Endereço da obra |
| identificacao_documento_tecnico | varchar | 30 | Identificação do documento técnico |
| documento_referencia | varchar | 50 | Documento de referência |
| pedido | varchar | 50 | Pedido |
| informacoes_complementares_servico | text | 2000 | Informações complementares do serviço |

### 8. Tributos Federais Detalhados

| Campo | Tipo | TAM | Descrição |
|-------|------|-----|-----------|
| base_calculo_pis_cofins | decimal(15,2) | 1-15V2 | Base de cálculo para PIS/COFINS |
| aliquota_pis | decimal(5,2) | 1-5V2 | Alíquota do PIS |
| aliquota_cofins | decimal(5,2) | 1-5V2 | Alíquota do COFINS |

### 9. IBS/CBS na DPS

| Campo | Tipo | TAM | Descrição |
|-------|------|-----|-----------|
| dps_codigo_localidade_incidencia_ibs | varchar | 7 | Código da localidade de incidência para IBS/CBS na DPS |
| dps_descricao_localidade_incidencia_ibs | varchar | 150 | Descrição da localidade de incidência para IBS/CBS na DPS |
| dps_indicador_operacao_ibs | varchar | 100 | Indicador de operação para IBS/CBS na DPS |
| dps_cst_ibs | varchar | 10 | CST para IBS/CBS na DPS |
| dps_classificacao_tributaria_ibs | varchar | 100 | Classificação tributária para IBS/CBS na DPS |
| dps_percentual_redutor_ibs | decimal(5,2) | 1-5V2 | Percentual redutor para IBS/CBS na DPS |
| dps_base_calculo_ibs | decimal(15,2) | 1-15V2 | Base de cálculo para IBS/CBS na DPS |
| dps_percentual_ibs_uf | decimal(5,2) | 1-5V2 | Percentual IBS UF na DPS |
| dps_aliquota_efetiva_ibs_uf | decimal(5,2) | 1-5V2 | Alíquota efetiva IBS UF na DPS |
| dps_valor_total_nf_ibs | decimal(15,2) | 1-15V2 | Valor total NF para IBS na DPS |
| dps_valor_total_ibs | decimal(15,2) | 1-15V2 | Valor total IBS na DPS |
| dps_valor_total_cbs | decimal(15,2) | 1-15V2 | Valor total CBS na DPS |

