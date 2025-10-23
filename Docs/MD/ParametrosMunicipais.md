# Documentação Técnica dos Parâmetros Municipais - Sistema Nacional NFS-e

Esta documentação técnica descreve os dados, esquemas, endpoints e padrões técnicos das APIs relacionadas aos Parâmetros Municipais do Sistema Nacional NFS-e, baseada nos arquivos OpenAPI fornecidos para os ambientes de Homologação (Produção Restrita) e Produção. Inclui informações detalhadas para desenvolvedores, como integração, exemplos de código, tratamento de erros e melhores práticas.

## Visão Geral

Os Parâmetros Municipais são configurações fiscais definidas por cada município para a tributação do ISSQN (Imposto Sobre Serviços de Qualquer Natureza). Eles incluem alíquotas, convênios, regimes especiais, retenções e benefícios, essenciais para o cálculo correto de impostos em NFS-e.

### Módulos Relacionados
- **NFS-e**: Nota Fiscal de Serviços Eletrônica – Utiliza parâmetros para cálculo de impostos.
- **Contribuintes ISSQN**: Acesso aos parâmetros para emissão de NFS-e.
- **Fisco (CGNFSe, RFB, Municípios)**: Gestão e consulta de parâmetros.

Ambientes:
- **Homologação**: Para testes (producaorestrita.nfse.gov.br).
- **Produção**: Ambiente oficial (nfse.gov.br).

## Requisitos de Uso

- **Certificado Digital**: Tipo e-CNPJ, A1 ou A3, emitido por Autoridade Certificadora ICP-Brasil, com "Autenticação do Cliente".
- **Autorização**: CNPJ deve ter permissão concedida pelo Comitê Gestor da NFS-e.
- **Protocolo**: TLS 1.2 ou superior com autenticação mútua.

## Padrões Técnicos

- **Protocolo de Comunicação**: TLS 1.2+ com autenticação mútua.
- **Formato de Troca de Mensagens**: JSON.
- **Formato dos Documentos**: XML 1.0 (https://www.w3.org/TR/REC-xml).
- **Codificação de Caracteres**: UTF-8.
- **Certificado Digital**: ICP-Brasil, A1/A3, CNPJ.
- **Assinatura XML**: XMLDSIG (https://www.w3.org/TR/xmldsig-core/).
- **Compactação**: GZip com base64binary.

## Integração para Desenvolvedores

### Passos de Integração
1. **Obter Certificado Digital**: Adquira um certificado e-CNPJ válido junto a uma Autoridade Certificadora ICP-Brasil. Garanta que o CNPJ tenha autorização para acessar as APIs (contate o Comitê Gestor da NFS-e).
2. **Configurar Ambiente**: Use URLs de Homologação para testes e Produção para produção. Configure seu cliente HTTP para autenticação mútua TLS.
3. **Preparar Dados**: Para consultas, prepare parâmetros como código do município, código do serviço e competência.
4. **Fazer Requisições**: Use bibliotecas HTTP que suportem certificados cliente (ex: requests em Python, axios em Node.js).
5. **Processar Respostas**: Parse JSON, verifique StatusProcessamento e trate erros/alertas.

### Autenticação Prática
- Instale o certificado em seu servidor/aplicação.
- Em Python (requests): Passe `cert=('cert.pem', 'key.pem')` e `verify=True` para TLS.
- Em Node.js (axios): Use `httpsAgent` com certificado.
- Teste em Homologação primeiro; erros comuns: certificado expirado (E3317), não autorizado.

### Tratamento de Erros Detalhado
- **Códigos de Status HTTP**: 200 (sucesso), 400 (erro cliente), 404 (não encontrado).
- **StatusProcessamento**:
  - PROCESSADO_COM_SUCESSO: Sucesso.
  - PROCESSADO_COM_ERROS: Sucesso com alertas (verifique Alertas).
  - FALHA_NO_PROCESSAMENTO: Erro crítico (verifique Erros).
  - NENHUM_DOCUMENTO_LOCALIZADO: Sem dados.
  - REJEICAO: Rejeitado (erros em Erros).
- **Erros Comuns**:
  - E3317: Certificado expirado.
  - Outros: Verifique Descricao em MensagemProcessamento.
- **Tratamento**: Logue erros, tente novamente após correção (ex: renovar certificado).

### Melhores Práticas
- **Segurança**: Armazene certificados em locais seguros. Use HTTPS sempre.
- **Performance**: Cacheie parâmetros frequentemente consultados para reduzir requisições.
- **Logs**: Registre todas as requisições/respostas, incluindo códigos de município e timestamps.
- **Testes**: Use Homologação para validar integração antes de Produção.
- **Versionamento**: Monitore VersaoAplicativo nas respostas para compatibilidade.
- **Rate Limiting**: Respeite limites de requisições; implemente backoff exponencial em erros.
- **Validação**: Valide códigos de município e serviço antes de consultas.
- **Monitoramento**: Implemente alertas para falhas consecutivas.

## Exemplos de Código

### Python (usando requests para Consulta Alíquota)

```python
import requests

# Requisição para consultar alíquota
url = 'https://www.producaorestrita.nfse.gov.br/parametros_municipais/1234567/010101/202510/aliquota'
response = requests.get(url, cert=('cert.pem', 'key.pem'), verify=True)

if response.status_code == 200:
    data = response.json()
    print(f"Alíquota: {data['aliquota']}%")
else:
    print(f"Erro: {response.status_code} - {response.text}")
```

### JavaScript (Node.js com axios para Consulta Convênio)

```javascript
const axios = require('axios');
const https = require('https');
const fs = require('fs');

// Configurar agente HTTPS com certificado
const agent = new https.Agent({
  cert: fs.readFileSync('cert.pem'),
  key: fs.readFileSync('key.pem'),
  ca: fs.readFileSync('ca.pem'),  // Se necessário
});

const url = 'https://www.producaorestrita.nfse.gov.br/parametros_municipais/1234567/convenio';

axios.get(url, { httpsAgent: agent })
  .then(response => {
    console.log('Convênio:', response.data);
  })
  .catch(error => {
    console.error('Erro:', error.response.data);
  });
```

### Curl para Consulta Retenções

```bash
curl -X GET "https://www.producaorestrita.nfse.gov.br/parametros_municipais/1234567/202510/retencoes" \
  --cert cert.pem --key key.pem --cacert ca.pem \
  -H "Accept: application/json"
```

## Modelos de Dados (Schemas)

### AliquotaISSQN
Informações sobre a alíquota do ISSQN.

- **CodigoMunicipio** (integer, int32): Código IBGE do município.
- **CodigoServico** (string): Código do serviço (ex: "010101").
- **Competencia** (string): Competência (AAAAMM).
- **Aliquota** (number): Percentual da alíquota (ex: 0.05 para 5%).

### ConvenioMunicipal
Parâmetros do convênio de um município.

- **CodigoMunicipio** (integer, int32): Código IBGE.
- **Convenio** (object): Detalhes do convênio.
  - **TipoConvenio** (string): Tipo (ex: "NORMAL").
  - **DataInicio** (string, date): Data de início.
  - **DataFim** (string, date): Data de fim.

### RegimeEspecial
Parâmetros de regimes especiais de tributação.

- **CodigoMunicipio** (integer, int32): Código IBGE.
- **CodigoServico** (string): Código do serviço.
- **Competencia** (string): Competência.
- **Regime** (string): Tipo de regime (ex: "MICROEMPRESA").

### RetencaoISSQN
Parâmetros para retenções do ISSQN.

- **CodigoMunicipio** (integer, int32): Código IBGE.
- **Competencia** (string): Competência.
- **Retencao** (object): Detalhes da retenção.
  - **TipoRetencao** (string): Tipo (ex: "TOMADOR").
  - **Percentual** (number): Percentual de retenção.

### BeneficioMunicipal
Parâmetros de um benefício municipal.

- **CodigoMunicipio** (integer, int32): Código IBGE.
- **NumeroBeneficio** (string): Número do benefício.
- **Competencia** (string): Competência.
- **Beneficio** (object): Detalhes do benefício.
  - **Descricao** (string): Descrição.
  - **Reducao** (number): Percentual de redução.

### HistoricoAliquotas
Histórico de alíquotas do ISSQN.

- **CodigoMunicipio** (integer, int32): Código IBGE.
- **CodigoServico** (string): Código do serviço.
- **Historico** (array of AliquotaISSQN): Lista de alíquotas por competência.

### MensagemProcessamento
Mensagem de processamento (alerta ou erro).

- **Mensagem** (object, additionalProperties: false): Mensagem genérica.
- **Parametros** (array of string, nullable): Parâmetros.
- **Codigo** (string, nullable): Código da mensagem.
- **Descricao** (string, nullable): Descrição.
- **Complemento** (string, nullable): Complemento.

## Endpoints

### APIs para Contribuintes do ISSQN

**Base URL Homologação**: https://www.producaorestrita.nfse.gov.br/swagger/contribuintesissqn/#/  
**Base URL Produção**: https://www.nfse.gov.br/swagger/contribuintesissqn/#/  

#### Parâmetros Municipais
- **GET /parametros_municipais/{codigoMunicipio}/{codigoServico}/{competencia}/aliquota**: Retorna a alíquota do ISSQN parametrizada de um município. Response: AliquotaISSQN.
- **GET /parametros_municipais/{codigoMunicipio}/{codigoServico}/historicoaliquotas**: Retorna o histórico de alíquotas do ISSQN parametrizadas. Response: HistoricoAliquotas.
- **GET /parametros_municipais/{codigoMunicipio}/convenio**: Retorna os parâmetros do convênio de um município. Response: ConvenioMunicipal.
- **GET /parametros_municipais/{codigoMunicipio}/{codigoServico}/{competencia}/regimes_especiais**: Retorna os parâmetros de regimes especiais de tributação. Response: RegimeEspecial.
- **GET /parametros_municipais/{codigoMunicipio}/{competencia}/retencoes**: Retorna os parâmetros para retenções do ISSQN de um município. Response: RetencaoISSQN.
- **GET /parametros_municipais/{codigoMunicipio}/{numeroBeneficio}/{competencia}/beneficio**: Retorna os parâmetros de um número de benefício municipal. Response: BeneficioMunicipal.

### APIs para o Fisco (CGNFSe, RFB, Municípios)

**Base URL Homologação**: https://www.producaorestrita.nfse.gov.br/swagger/fisco/  
**Base URL Produção**: https://www.nfse.gov.br/swagger/fisco/  

#### Parâmetros Municipais
- **GET /parametros_municipais/{codigoMunicipio}/{codigoServico}/{competencia}/aliquota**: Retorna a alíquota do ISSQN parametrizada de um município. Response: AliquotaISSQN.
- **GET /parametros_municipais/{codigoMunicipio}/{codigoServico}/historicoaliquotas**: Retorna o histórico de alíquotas do ISSQN parametrizadas. Response: HistoricoAliquotas.
- **GET /parametros_municipais/{codigoMunicipio}/convenio**: Retorna os parâmetros do convênio de um município. Response: ConvenioMunicipal.
- **GET /parametros_municipais/{codigoMunicipio}/{codigoServico}/{competencia}/regimes_especiais**: Retorna os parâmetros de regimes especiais de tributação. Response: RegimeEspecial.
- **GET /parametros_municipais/{codigoMunicipio}/{competencia}/retencoes**: Retorna os parâmetros para retenções do ISSQN de um município. Response: RetencaoISSQN.
- **GET /parametros_municipais/{codigoMunicipio}/{numeroBeneficio}/{competencia}/beneficio**: Retorna os parâmetros de um número de benefício municipal. Response: BeneficioMunicipal.

## Exemplos

### Exemplo de Consulta Alíquota (200)
```json
{
  "codigoMunicipio": 1234567,
  "codigoServico": "010101",
  "competencia": "202510",
  "aliquota": 0.05
}
```

### Exemplo de Consulta Convênio (200)
```json
{
  "codigoMunicipio": 1234567,
  "convenio": {
    "tipoConvenio": "NORMAL",
    "dataInicio": "2025-01-01",
    "dataFim": "2025-12-31"
  }
}
```

### Exemplo de Consulta Retenções (200)
```json
{
  "codigoMunicipio": 1234567,
  "competencia": "202510",
  "retencao": {
    "tipoRetencao": "TOMADOR",
    "percentual": 0.05
  }
}
```

## Glossário

- **Alíquota**: Percentual de imposto aplicado sobre um serviço ou produto.
- **API**: Application Programming Interface – Conjunto de regras e protocolos para interação entre aplicações de software.
- **Alertas**: Avisos não críticos retornados em respostas de processamento, indicando situações que não impedem o sucesso da operação.
- **Array**: Lista ordenada de elementos em estruturas de dados como JSON.
- **Autenticação Mútua**: Processo onde cliente e servidor se autenticam mutuamente usando certificados digitais.
- **Autoridade Certificadora**: Entidade responsável por emitir e gerenciar certificados digitais.
- **Backoff Exponencial**: Estratégia de retry onde o intervalo entre tentativas aumenta exponencialmente.
- **Benefício**: Incentivo fiscal concedido a contribuintes, como reduções ou isenções.
- **Certificado Digital**: Documento eletrônico usado para autenticação e assinatura digital, emitido por ICP-Brasil.
- **CGNFSe**: Comitê Gestor Nacional da NFS-e – Órgão responsável pela gestão e regulamentação do Sistema Nacional NFS-e.
- **Competência**: Período fiscal ao qual se refere uma operação ou parâmetro.
- **Complemento**: Detalhes adicionais em mensagens de processamento.
- **Código**: Identificador numérico ou alfanumérico de uma mensagem de processamento.
- **Data/Hora Processamento**: Timestamp indicando quando uma operação foi processada.
- **Descrição**: Texto explicativo de uma mensagem de processamento.
- **Endpoint**: Ponto de acesso específico em uma API para realizar operações.
- **Enum**: Tipo de dado que representa um conjunto fixo de valores possíveis.
- **Erros**: Problemas críticos retornados em respostas de processamento, indicando falhas.
- **GZip**: Algoritmo de compressão de dados, usado para reduzir o tamanho de arquivos XML.
- **HTTP**: HyperText Transfer Protocol – Protocolo para comunicação na web.
- **HTTPS**: HTTP Secure – Versão segura do HTTP usando TLS.
- **IBGE**: Instituto Brasileiro de Geografia e Estatística – Órgão responsável por códigos de municípios e regiões.
- **ICP-Brasil**: Infraestrutura de Chaves Públicas Brasileira – Sistema nacional de certificação digital.
- **Integer**: Tipo de dado representando números inteiros.
- **ISSQN**: Imposto Sobre Serviços de Qualquer Natureza – Tributo municipal sobre serviços.
- **JSON**: JavaScript Object Notation – Formato leve para troca de dados.
- **Log**: Registro de eventos e operações para auditoria e depuração.
- **Mensagem Processamento**: Comunicação estruturada sobre o resultado de uma operação.
- **Monitoramento**: Supervisão contínua de sistemas e processos para detectar anomalias.
- **NFS-e**: Nota Fiscal de Serviços Eletrônica – Documento fiscal para serviços.
- **Nullable**: Indica que um campo pode ter valor nulo.
- **Object**: Estrutura de dados composta por pares chave-valor em JSON.
- **OpenAPI**: Especificação para descrever APIs REST de forma padronizada.
- **Origem Dados**: Fonte das informações cadastrais (ex: Parâmetros Municipais).
- **Paginação**: Técnica para dividir grandes conjuntos de dados em páginas menores.
- **Parâmetros**: Valores variáveis incluídos em mensagens de processamento.
- **Path Parameter**: Parâmetro incluído no caminho da URL de um endpoint.
- **Query Parameter**: Parâmetro incluído na query string da URL.
- **Rate Limiting**: Controle de taxa de requisições para evitar sobrecarga.
- **Regime Especial**: Tributação diferenciada aplicada a certos contribuintes ou serviços.
- **Request Body**: Corpo da requisição HTTP contendo dados enviados ao servidor.
- **Response**: Resposta retornada pelo servidor a uma requisição HTTP.
- **REST**: Representational State Transfer – Estilo arquitetural para APIs web.
- **Retenção**: Dedução de imposto na fonte pelo tomador do serviço.
- **Schema**: Estrutura ou modelo de dados definindo formato e tipos.
- **Status Code**: Código numérico indicando o resultado de uma requisição HTTP.
- **Status Processamento**: Estado geral do processamento de uma operação.
- **String**: Tipo de dado representando cadeias de caracteres.
- **Swagger**: Ferramenta para documentar e testar APIs baseadas em OpenAPI.
- **Timestamp**: Marca temporal representando data e hora.
- **TLS**: Transport Layer Security – Protocolo para comunicação segura na web.
- **UF**: Unidade Federativa – Estado brasileiro.
- **UTF-8**: Unicode Transformation Format 8-bit – Codificação de caracteres universal.
- **Validação**: Verificação de conformidade de dados com regras e schemas.
- **Versão Aplicativo**: Número de versão do software ou aplicação.
- **XML**: Extensible Markup Language – Linguagem para marcação de dados estruturados.
- **XMLDSIG**: XML Digital Signature – Padrão para assinatura digital de documentos XML.

Para mais detalhes, consulte os arquivos OpenAPI originais em `Api/ParametrosMunicipais/Homologação/` e `Api/ParametrosMunicipais/Produção/`, ou a documentação online nos URLs fornecidos em Documentação.md.
