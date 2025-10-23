# Documentação Técnica do Ambiente de Dados Nacional (ADN) - Sistema Nacional NFS-e

Esta documentação técnica descreve os dados, esquemas, endpoints e padrões técnicos das APIs do Ambiente de Dados Nacional (ADN) do Sistema Nacional NFS-e, baseada nos arquivos OpenAPI fornecidos para os ambientes de Homologação (Produção Restrita) e Produção. Inclui informações detalhadas para desenvolvedores, como integração, exemplos de código, tratamento de erros e melhores práticas.

## Visão Geral

O Ambiente de Dados Nacional (ADN) é um módulo centralizado do Sistema Nacional NFS-e responsável pelo armazenamento e distribuição de documentos fiscais. Ele integra várias APIs para gestão de Notas Fiscais de Serviços Eletrônicas, focando na distribuição e recepção de Documentos Fiscais Eletrônicos (DF-e) e eventos relacionados.

### Módulos Relacionados
- **DFe**: Distribuição de Documentos Fiscais Eletrônicos.
- **Eventos**: Registro e distribuição de eventos vinculados a NFS-e.
- **NFS-e**: Consulta de Notas Fiscais de Serviços Eletrônicas.

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
3. **Preparar Dados**: Para Recepção, gere XML válido, assine com XMLDSIG, compacte com GZip e encode em base64. Para Consultas, prepare parâmetros de query.
4. **Fazer Requisições**: Use bibliotecas HTTP que suportem certificados cliente (ex: requests em Python, axios em Node.js).
5. **Processar Respostas**: Parse JSON/XML, verifique StatusProcessamento e trate erros/alertas.
6. **Paginação/NSU**: Para Distribuição, use NSU para paginação incremental, armazenando o último NSU processado.

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
- **Tratamento**: Logue erros, tente novamente após correção (ex: renovar certificado). Para Distribuição, aguarde antes de nova consulta se NSU alto.

### Melhores Práticas
- **Segurança**: Armazene certificados em locais seguros. Use HTTPS sempre.
- **Performance**: Para Distribuição, processe lotes incrementalmente; evite consultas frequentes.
- **Logs**: Registre todas as requisições/respostas, incluindo NSU e timestamps.
- **Testes**: Use Homologação para validar integração antes de Produção.
- **Versionamento**: Monitore VersaoAplicativo nas respostas para compatibilidade.
- **Rate Limiting**: Respeite limites de requisições; implemente backoff exponencial em erros.
- **Validação**: Valide XML contra schemas antes de envio.
- **Monitoramento**: Implemente alertas para falhas consecutivas.

## Exemplos de Código

### Python (usando requests para Recepção de DFe)

```python
import requests
import base64
import gzip
from xml.etree.ElementTree import Element, SubElement, tostring

# Gerar XML DFe simples (exemplo fictício)
root = Element('DFe')
doc = SubElement(root, 'Documento')
SubElement(doc, 'ChaveAcesso').text = '12345678901234567890123456789012345678901234'
xml_str = tostring(root, encoding='utf-8').decode('utf-8')

# Compactar e base64
compressed = gzip.compress(xml_str.encode('utf-8'))
encoded = base64.b64encode(compressed).decode('utf-8')

# Requisição
url = 'https://adn.producaorestrita.nfse.gov.br/dfe'
headers = {'Content-Type': 'application/json'}
data = {'data': encoded}  # Ajuste conforme schema

response = requests.post(url, json=data, cert=('cert.pem', 'key.pem'), verify=True)
print(response.json())
```

### JavaScript (Node.js com axios para Consulta de DFe)

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

const url = 'https://adn.producaorestrita.nfse.gov.br/municipios/dfe/1';
const params = {};

axios.get(url, { params, httpsAgent: agent })
  .then(response => {
    console.log(response.data);
  })
  .catch(error => {
    console.error(error.response.data);
  });
```

### Curl para Distribuição de Eventos

```bash
curl -X GET "https://adn.producaorestrita.nfse.gov.br/municipios/NFSe/12345678901234567890123456789012345678901234/Eventos" \
  --cert cert.pem --key key.pem --cacert ca.pem \
  -H "Accept: application/json"
```

## Modelos de Dados (Schemas)

### DistribuicaoNSU
Objeto representando um documento de distribuição.

- **NSU** (integer, int64, nullable): Número Sequencial Único.
- **ChaveAcesso** (string, nullable): Chave de acesso da NFS-e.
- **TipoDocumento** (enum: NENHUM, DPS, PEDIDO_REGISTRO_EVENTO, NFSE, EVENTO, CNC): Tipo do documento.
- **TipoEvento** (enum: CANCELAMENTO, SOLICITACAO_CANCELAMENTO_ANALISE_FISCAL, etc., nullable): Tipo do evento.
- **ArquivoXml** (string, nullable): Arquivo XML compactado em base64.
- **DataHoraGeracao** (string, date-time, nullable): Data e hora da geração.

### LoteDistribuicaoNSUResponse
Resposta para consultas de distribuição.

- **StatusProcessamento** (enum: REJEICAO, NENHUM_DOCUMENTO_LOCALIZADO, DOCUMENTOS_LOCALIZADOS): Status.
- **LoteDFe** (array of DistribuicaoNSU, nullable): Lista de documentos.
- **Alertas** (array of MensagemProcessamento, nullable): Alertas.
- **Erros** (array of MensagemProcessamento, nullable): Erros.
- **TipoAmbiente** (enum: PRODUCAO, HOMOLOGACAO): Ambiente.
- **VersaoAplicativo** (string, nullable): Versão do aplicativo.
- **DataHoraProcessamento** (string, date-time): Data/hora processamento.

### MensagemProcessamento
Mensagem de processamento (alerta ou erro).

- **Mensagem** (object, additionalProperties: false): Mensagem genérica.
- **Parametros** (array of string, nullable): Parâmetros.
- **Codigo** (string, nullable): Código da mensagem.
- **Descricao** (string, nullable): Descrição.
- **Complemento** (string, nullable): Complemento.

### RecepcaoRequest
Requisição para recepção de lote.

- **LoteXmlGZipB64** (array of string, nullable): Lote de XMLs compactados em base64.

### RecepcaoResponseDocumento
Resposta para documento individual.

- **ChaveAcesso** (string, nullable): Chave de acesso.
- **NsuRecepcao** (string, nullable): NSU de recepção.
- **StatusProcessamento** (string, nullable): Status.
- **Alertas** (array of MensagemProcessamento, nullable): Alertas.
- **Erros** (array of MensagemProcessamento, nullable): Erros.

### RecepcaoResponseLote
Resposta para lote de recepção.

- **Lote** (array of RecepcaoResponseDocumento, nullable): Lote processado.
- **TipoAmbiente** (enum: PRODUCAO, HOMOLOGACAO): Ambiente.
- **VersaoAplicativo** (string, nullable): Versão.
- **DataHoraProcessamento** (string, date-time): Data/hora.

## Endpoints

### API ADN DFe

**Base URL Homologação**: /dfe  
**Base URL Produção**: /dfe  

#### POST /dfe
Recepciona um lote de Documentos Fiscais Eletrônicos.

**Request Body**: string (XML DFe compactado em base64).

**Respostas**:
- 200: RecepcaoResponse com sucesso.
- 400: RecepcaoResponse com falha.

#### GET /municipios/dfe/{NSU}
Retorna um lote (até 50) de Documentos Fiscais de Serviço a partir do NSU informado.

**Parâmetros Path**:
- **NSU** (integer, int64, required): Número Sequencial Único.

**Respostas**:
- 200: LoteDFeResponse com documentos.
- 404: LoteDFeResponse com status "NENHUM_DOCUMENTO_LOCALIZADO".

### API ADN Eventos

**Base URL Homologação**: /municipios/NFSe  
**Base URL Produção**: /municipios/NFSe  

#### GET /{ChaveAcesso}/Eventos
Retorna um lote (até 50) de Documentos Fiscais de Serviço (tipo Evento) vinculados à chave de acesso.

**Parâmetros Path**:
- **ChaveAcesso** (string, required): Chave de acesso da NFS-e (50 posições).

**Respostas**:
- 200: LoteDFeResponse com eventos.
- 404: Sem eventos localizados.

## Exemplos

### Exemplo de Resposta de Distribuição de DFe (200)
```json
{
  "LoteDFe": [
    {
      "ChaveAcesso": "12345678901234567890123456789012345678901234",
      "ArquivoXml": "H4sIAAAAAAAAA7OpyM0xtNPT07PRB7MAjYPXARAAAAA=",
      "DataHoraProcessamento": "2025-10-17T10:28:54.8637734-03:00"
    }
  ],
  "StatusProcessamento": "DOCUMENTOS_LOCALIZADOS",
  "Alertas": [],
  "Erros": [],
  "TipoAmbiente": "HOMOLOGACAO",
  "DataHoraProcessamento": "2025-10-17T10:28:54.8638201-03:00"
}
```

### Exemplo de Recepção de DFe (200)
```json
{
  "ChaveMovimentacao": "MOV1234567890",
  "StatusProcessamento": "PROCESSADO_COM_SUCESSO",
  "Alertas": [],
  "Erros": [],
  "TipoAmbiente": "HOMOLOGACAO",
  "DataHoraProcessamento": "2025-10-17T10:28:11.4754614-03:00"
}
```

## Glossário

- **ADN**: Ambiente de Dados Nacional – Ambiente centralizado para armazenamento e distribuição de documentos fiscais no Sistema Nacional NFS-e.
- **API**: Application Programming Interface – Conjunto de regras e protocolos para interação entre aplicações de software.
- **Alertas**: Avisos não críticos retornados em respostas de processamento, indicando situações que não impedem o sucesso da operação.
- **Array**: Lista ordenada de elementos em estruturas de dados como JSON.
- **Autenticação Mútua**: Processo onde cliente e servidor se autenticam mutuamente usando certificados digitais.
- **Autoridade Certificadora**: Entidade responsável por emitir e gerenciar certificados digitais.
- **Backoff Exponencial**: Estratégia de retry onde o intervalo entre tentativas aumenta exponencialmente.
- **Base64**: Método de codificação binária para representação de dados em texto ASCII.
- **Certificado Digital**: Documento eletrônico usado para autenticação e assinatura digital, emitido por ICP-Brasil.
- **CGNFSe**: Comitê Gestor Nacional da NFS-e – Órgão responsável pela gestão e regulamentação do Sistema Nacional NFS-e.
- **Chave de Acesso**: Identificador único de 50 posições para uma NFS-e.
- **Chave de Movimentação**: Identificador único para movimentações no ADN.
- **Competência**: Período fiscal ao qual se refere uma operação ou parâmetro.
- **Complemento**: Detalhes adicionais em mensagens de processamento.
- **Código**: Identificador numérico ou alfanumérico de uma mensagem de processamento.
- **Data/Hora Processamento**: Timestamp indicando quando uma operação foi processada.
- **Descrição**: Texto explicativo de uma mensagem de processamento.
- **DFe**: Documento Fiscal Eletrônico – Documento fiscal em formato eletrônico.
- **Endpoint**: Ponto de acesso específico em uma API para realizar operações.
- **Enum**: Tipo de dado que representa um conjunto fixo de valores possíveis.
- **Erros**: Problemas críticos retornados em respostas de processamento, indicando falhas.
- **Eventos**: Registros de ocorrências relacionadas a NFS-e, como cancelamentos ou correções.
- **GZip**: Algoritmo de compressão de dados, usado para reduzir o tamanho de arquivos XML.
- **HTTP**: HyperText Transfer Protocol – Protocolo para comunicação na web.
- **HTTPS**: HTTP Secure – Versão segura do HTTP usando TLS.
- **ICP-Brasil**: Infraestrutura de Chaves Públicas Brasileira – Sistema nacional de certificação digital.
- **Integer**: Tipo de dado representando números inteiros.
- **JSON**: JavaScript Object Notation – Formato leve para troca de dados.
- **Log**: Registro de eventos e operações para auditoria e depuração.
- **Mensagem Processamento**: Comunicação estruturada sobre o resultado de uma operação.
- **Monitoramento**: Supervisão contínua de sistemas e processos para detectar anomalias.
- **NFS-e**: Nota Fiscal de Serviços Eletrônica – Documento fiscal para serviços.
- **NSU**: Número Sequencial Único – Identificador incremental para documentos.
- **Número Sequencial de Evento**: Contador sequencial para eventos relacionados a uma NFS-e.
- **Nullable**: Indica que um campo pode ter valor nulo.
- **Object**: Estrutura de dados composta por pares chave-valor em JSON.
- **OpenAPI**: Especificação para descrever APIs REST de forma padronizada.
- **Origem Dados**: Fonte das informações cadastrais (ex: ADN).
- **Paginação**: Técnica para dividir grandes conjuntos de dados em páginas menores.
- **Parâmetros**: Valores variáveis incluídos em mensagens de processamento.
- **Path Parameter**: Parâmetro incluído no caminho da URL de um endpoint.
- **Query Parameter**: Parâmetro incluído na query string da URL.
- **Rate Limiting**: Controle de taxa de requisições para evitar sobrecarga.
- **Request Body**: Corpo da requisição HTTP contendo dados enviados ao servidor.
- **Response**: Resposta retornada pelo servidor a uma requisição HTTP.
- **REST**: Representational State Transfer – Estilo arquitetural para APIs web.
- **Schema**: Estrutura ou modelo de dados definindo formato e tipos.
- **Status Code**: Código numérico indicando o resultado de uma requisição HTTP.
- **Status Processamento**: Estado geral do processamento de uma operação.
- **String**: Tipo de dado representando cadeias de caracteres.
- **Swagger**: Ferramenta para documentar e testar APIs baseadas em OpenAPI.
- **Timestamp**: Marca temporal representando data e hora.
- **Tipo de Evento**: Categoria específica de um evento relacionado a NFS-e.
- **TLS**: Transport Layer Security – Protocolo para comunicação segura na web.
- **UTF-8**: Unicode Transformation Format 8-bit – Codificação de caracteres universal.
- **Validação**: Verificação de conformidade de dados com regras e schemas.
- **Versão Aplicativo**: Número de versão do software ou aplicação.
- **XML**: Extensible Markup Language – Linguagem para marcação de dados estruturados.
- **XMLDSIG**: XML Digital Signature – Padrão para assinatura digital de documentos XML.

Para mais detalhes, consulte os arquivos OpenAPI originais em `Api/ADN/Homologação/` e `Api/ADN/Produção/`, ou a documentação online nos URLs fornecidos em Documentação.md.
