# Documentação Técnica dos Dados das APIs CNC - Sistema Nacional NFS-e

Esta documentação técnica descreve os dados, esquemas, endpoints e padrões técnicos das APIs do Cadastro Nacional de Contribuintes (CNC) do Sistema Nacional NFS-e, baseada nos arquivos OpenAPI fornecidos para os ambientes de Homologação (Produção Restrita) e Produção. Inclui informações detalhadas para desenvolvedores, como integração, exemplos de código, tratamento de erros e melhores práticas.

## Visão Geral

O Cadastro Nacional de Contribuintes (CNC) é um módulo do Sistema Nacional NFS-e responsável pela gestão de dados de contribuintes, incluindo distribuição, recepção e consulta de informações cadastrais. Ele integra várias APIs para gestão de Notas Fiscais de Serviços Eletrônicas, focando no cadastro nacional de contribuintes do ISSQN.

### Módulos Relacionados
- **Distribuição CNC**: Distribuição de dados de cadastros e movimentações.
- **Recepção CNC**: Recepção de dados para cadastro/atualização de contribuintes.
- **Consulta CNC**: Consulta de informações atuais de contribuintes.

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
- **Formato dos Documentos CNC**: XML 1.0 (https://www.w3.org/TR/REC-xml).
- **Codificação de Caracteres**: UTF-8.
- **Certificado Digital**: ICP-Brasil, A1/A3, CNPJ.
- **Assinatura XML**: XMLDSIG (https://www.w3.org/TR/xmldsig-core/).
- **Compactação**: GZip com base64binary.

## Integração para Desenvolvedores

### Passos de Integração
1. **Obter Certificado Digital**: Adquira um certificado e-CNPJ válido junto a uma Autoridade Certificadora ICP-Brasil. Garanta que o CNPJ tenha autorização para acessar as APIs (contate o Comitê Gestor da NFS-e).
2. **Configurar Ambiente**: Use URLs de Homologação para testes e Produção para produção. Configure seu cliente HTTP para autenticação mútua TLS.
3. **Preparar Dados**: Para Recepção, gere XML CNC válido, assine com XMLDSIG, compacte com GZip e encode em base64. Para Consultas, prepare parâmetros de query.
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
- **Validação**: Valide XML CNC contra schemas antes de envio.
- **Monitoramento**: Implemente alertas para falhas consecutivas.

## Exemplos de Código

### Python (usando requests para Recepção CNC)

```python
import requests
import base64
import gzip
from xml.etree.ElementTree import Element, SubElement, tostring

# Gerar XML CNC simples (exemplo fictício)
root = Element('CNC')
contrib = SubElement(root, 'Contribuinte')
SubElement(contrib, 'CNPJ').text = '99999999999999'
xml_str = tostring(root, encoding='utf-8').decode('utf-8')

# Compactar e base64
compressed = gzip.compress(xml_str.encode('utf-8'))
encoded = base64.b64encode(compressed).decode('utf-8')

# Requisição
url = 'https://adn.producaorestrita.nfse.gov.br/cnc/CNC'
headers = {'Content-Type': 'application/json'}
data = {'data': encoded}  # Ajuste conforme schema

response = requests.post(url, json=data, cert=('cert.pem', 'key.pem'), verify=True)
print(response.json())
```

### JavaScript (Node.js com axios para Consulta CNC)

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

const url = 'https://adn.producaorestrita.nfse.gov.br/cnc/consulta/cad';
const params = {
  codMunicipio: 1234567,
  inscricaoFederal: '99999999999999'
};

axios.get(url, { params, httpsAgent: agent })
  .then(response => {
    console.log(response.data);
  })
  .catch(error => {
    console.error(error.response.data);
  });
```

### Curl para Distribuição CNC

```bash
curl -X GET "https://adn.producaorestrita.nfse.gov.br/cnc/municipio/cad/NSU?NSUMovimento=1&NSUCadastro=1" \
  --cert cert.pem --key key.pem --cacert ca.pem \
  -H "Accept: application/json"
```

## Modelos de Dados (Schemas)

### DistribuicaoCNC
Objeto representando um documento de distribuição CNC.

- **NSU** (integer, int64, nullable): Número Sequencial Único.
- **ArquivoXml** (string, nullable): Arquivo XML compactado em base64.
- **DataHoraProcessamento** (string, date-time, nullable): Data e hora do processamento.

### LoteDistribuicaoCNCResponse
Resposta para consultas de distribuição.

- **TipoAmbiente** (enum: PRODUCAO, HOMOLOGACAO): Ambiente.
- **VersaoAplicativo** (string, nullable): Versão do aplicativo.
- **DataHoraProcessamento** (string, date-time): Data/hora processamento.
- **StatusProcessamento** (enum: REJEICAO, NENHUM_DOCUMENTO_LOCALIZADO, DOCUMENTOS_LOCALIZADOS): Status.
- **Alertas** (array of MensagemProcessamento, nullable): Alertas.
- **Erros** (array of MensagemProcessamento, nullable): Erros.
- **LoteDistribuicaoCNC** (array of DistribuicaoCNC, nullable): Lista de documentos.

### MensagemProcessamento
Mensagem de processamento (alerta ou erro).

- **Mensagem** (object, additionalProperties: false): Mensagem genérica.
- **Parametros** (array of string, nullable): Parâmetros.
- **Codigo** (string, nullable): Código da mensagem.
- **Descricao** (string, nullable): Descrição.
- **Complemento** (string, nullable): Complemento.

### RecepcaoResponse
Resposta para recepção de dados.

- **TipoAmbiente** (enum: PRODUCAO, HOMOLOGACAO): Ambiente.
- **VersaoAplicativo** (string, nullable): Versão.
- **DataHoraProcessamento** (string, date-time): Data/hora.
- **StatusProcessamento** (enum: PROCESSADO_COM_SUCESSO, PROCESSADO_COM_ERROS, FALHA_NO_PROCESSAMENTO): Status.
- **Alertas** (array of MensagemProcessamento, nullable): Alertas.
- **Erros** (array of MensagemProcessamento, nullable): Erros.
- **ChaveMovimentacao** (string, nullable): Chave da movimentação.

### ConsultaCNC
Objeto representando uma consulta de CNC.

- **NSU** (integer, int64, nullable): Número Sequencial Único.
- **ArquivoXml** (string, nullable): Arquivo XML.
- **DataHoraProcessamento** (string, date-time, nullable): Data/hora processamento.

### LoteConsultaCNCResponse
Resposta para consultas de CNC.

- **TipoAmbiente** (enum: PRODUCAO, HOMOLOGACAO): Ambiente.
- **VersaoAplicativo** (string, nullable): Versão do aplicativo.
- **DataHoraProcessamento** (string, date-time): Data/hora processamento.
- **StatusProcessamento** (enum: REJEICAO, NENHUM_DOCUMENTO_LOCALIZADO, DOCUMENTOS_LOCALIZADOS): Status.
- **Alertas** (array of MensagemProcessamento, nullable): Alertas.
- **Erros** (array of MensagemProcessamento, nullable): Erros.
- **LoteConsultaCNC** (array of ConsultaCNC, nullable): Lista de consultas.

### CadastroMunicipal
Informações de cadastro municipal de contribuinte.

- **OrigemDados** (string): Origem dos dados (ex: "CNC").
- **CodigoMunicipio** (integer, int32): Código IBGE do município.
- **TipoAmbiente** (enum: PRODUCAO, HOMOLOGACAO): Ambiente.
- **Id** (integer): Identificador único.
- **CodigoUF** (integer): Código da UF.
- **InfCad** (object): Informações do cadastro.
  - **Inscricao** (string): Inscrição federal (CPF/CNPJ).
  - **TpInscricao** (string): Tipo de inscrição (CPF/CNPJ).
  - **InscricaoMunicipal** (string): Inscrição municipal.
  - **DataInicioInscricaoMunicipal** (string, date-time): Data de início da inscrição municipal.
  - **DataApenasInicioInscricaoMunicipal** (string, date): Data de início (apenas data).
  - **RazaoSocial** (string): Razão social.
  - **InfoEndereco** (object): Informações de endereço.
    - **CEP** (string): CEP.
    - **Logradouro** (string): Logradouro.
    - **Numero** (string): Número.
    - **Complemento** (string): Complemento.
    - **Bairro** (string): Bairro.
  - **Telefone** (string): Telefone.
  - **Email** (string): Email.
  - **SituacaoEmissaoNFSe** (string): Situação de emissão (HABILITADO/NAO_HABILITADO).
  - **SituacaoCadastral** (string): Situação cadastral.
  - **MotivoSituacaoCadastral** (string): Motivo da situação cadastral.
- **DhCadastro** (string, date-time): Data/hora do cadastro.
- **DhAtualizacao** (string, date-time): Data/hora da atualização.
- **OpcaoSimplesNacional** (string): Opção pelo Simples Nacional.

## Endpoints

### API CNC Município (Distribuição)

**Base URL Homologação**: /cnc/municipio  
**Base URL Produção**: /cnc/municipio  

#### GET /cad/NSU
Consulta lote de até 50 documentos CNC de cadastros/movimentações.

**Parâmetros Query**:
- **NSUMovimento** (integer, int64): NSU a partir do qual obter movimentações.
- **NSUCadastro** (integer, int64): NSU a partir do qual obter cadastros.

**Respostas**:
- 200: LoteDistribuicaoCNCResponse com documentos.
- 404: LoteDistribuicaoCNCResponse com status "NENHUM_DOCUMENTO_LOCALIZADO".

#### GET /cad/{inscricaoFederal}/{NSUMovimento}
Consulta lote de até 50 documentos CNC de movimentações de um contribuinte.

**Parâmetros Path**:
- **inscricaoFederal** (string, required): CPF/CNPJ.
- **NSUMovimento** (integer, int64, required): NSU a partir do qual obter.

**Respostas**: Similar ao acima.

#### GET /mun/{codMunicipio}/{NSUMunicipio}
Consulta lote de até 50 documentos CNC de movimentações de um município.

**Parâmetros Path**:
- **codMunicipio** (integer, int32, required): Código IBGE.
- **NSUMunicipio** (integer, int64, required): NSU a partir do qual obter.

**Respostas**: Similar.

### API CNC Recepção

**Base URL Homologação**: /cnc  
**Base URL Produção**: /cnc  

#### POST /CNC
Recepciona contribuinte para cadastro/atualização.

**Request Body**: string (XML CNC compactado em base64).

**Respostas**:
- 200: RecepcaoResponse com sucesso.
- 400: RecepcaoResponse com falha.

### API CNC Consulta

**Base URL Homologação**: /cnc/consulta  
**Base URL Produção**: /cnc/consulta  

#### GET /cad
Consulta informações atuais de contribuintes.

**Parâmetros Query**:
- **codMunicipio** (integer, int32): Código IBGE.
- **inscricaoFederal** (string): CPF/CNPJ.
- **indicadorMunicipal** (string): Indicador municipal.

**Respostas**:
- 200: LoteConsultaCNCResponse com ListaCadastroMunicipal.
- 404: LoteConsultaCNCResponse sem documentos.
- 400: Com erros.

## Exemplos

### Exemplo de Resposta de Distribuição (200)
```json
{
  "LoteDistribuicaoCNC": [
    {
      "NSU": 1,
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

### Exemplo de Recepção (200)
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

### Exemplo de Consulta (200)
```json
{
  "ListaCadastroMunicipal": [
    {
      "OrigemDados": "CNC",
      "CodigoMunicipio": 1234567,
      "TipoAmbiente": "HOMOLOGACAO",
      "Id": 1,
      "CodigoUF": 12,
      "InfCad": {
        "Inscricao": "99999999999999",
        "TpInscricao": "CNPJ",
        "InscricaoMunicipal": "1234567",
        "DataInicioInscricaoMunicipal": "2025-10-21T09:45:25.9593398-03:00",
        "RazaoSocial": "Razao Social",
        "InfoEndereco": {
          "CEP": "12345678",
          "Logradouro": "Logradouro",
          "Numero": "123",
          "Complemento": "Complemento",
          "Bairro": "Bairro"
        },
        "Telefone": "1234567890",
        "Email": "email@email.com",
        "SituacaoEmissaoNFSe": "HABILITADO",
        "SituacaoCadastral": "1",
        "MotivoSituacaoCadastral": "Motivo"
      },
      "DhCadastro": "2025-10-21T09:45:25.959342-03:00",
      "DhAtualizacao": "2025-10-21T09:45:25.9593436-03:00",
      "OpcaoSimplesNacional": "NENHUM"
    }
  ],
  "StatusProcessamento": "DOCUMENTOS_LOCALIZADOS",
  "Alertas": [],
  "Erros": [],
  "TipoAmbiente": "HOMOLOGACAO",
  "VersaoAplicativo": "1.0.0.0",
  "DataHoraProcessamento": "2025-10-21T09:45:25.9594202-03:00"
}
```

## Glossário

- **ADN**: Ambiente de Dados Nacional – Ambiente centralizado para armazenamento e distribuição de documentos fiscais no Sistema Nacional NFS-e.
- **API**: Application Programming Interface – Conjunto de regras e protocolos para interação entre aplicações de software.
- **Alertas**: Avisos não críticos retornados em respostas de processamento, indicando situações que não impedem o sucesso da operação.
- **Alíquota**: Percentual de imposto aplicado sobre um serviço ou produto.
- **Array**: Lista ordenada de elementos em estruturas de dados como JSON.
- **Autenticação Mútua**: Processo onde cliente e servidor se autenticam mutuamente usando certificados digitais.
- **Autoridade Certificadora**: Entidade responsável por emitir e gerenciar certificados digitais.
- **Backoff Exponencial**: Estratégia de retry onde o intervalo entre tentativas aumenta exponencialmente.
- **Base64**: Método de codificação binária para representação de dados em texto ASCII.
- **Benefício**: Incentivo fiscal concedido a contribuintes, como reduções ou isenções.
- **Certificado Digital**: Documento eletrônico usado para autenticação e assinatura digital, emitido por ICP-Brasil.
- **CGNFSe**: Comitê Gestor Nacional da NFS-e – Órgão responsável pela gestão e regulamentação do Sistema Nacional NFS-e.
- **Chave de Acesso**: Identificador único de 50 posições para uma NFS-e.
- **Chave de Movimentação**: Identificador único para movimentações no CNC.
- **CNC**: Cadastro Nacional de Contribuintes – Módulo do Sistema Nacional NFS-e para gestão de dados de contribuintes.
- **CNPJ**: Cadastro Nacional da Pessoa Jurídica – Número de identificação para empresas.
- **Competência**: Período fiscal ao qual se refere uma operação ou parâmetro.
- **Complemento**: Detalhes adicionais em mensagens de processamento.
- **Código**: Identificador numérico ou alfanumérico de uma mensagem de processamento.
- **CPF**: Cadastro de Pessoas Físicas – Número de identificação para indivíduos.
- **Código**: Identificador numérico ou alfanumérico de uma mensagem de processamento.
- **DANFSe**: Documento Auxiliar da Nota Fiscal de Serviços Eletrônica – Versão legível da NFS-e.
- **Data/Hora Processamento**: Timestamp indicando quando uma operação foi processada.
- **Descrição**: Texto explicativo de uma mensagem de processamento.
- **DFe**: Documento Fiscal Eletrônico – Documento fiscal em formato eletrônico.
- **DPS**: Declaração de Prestação de Serviço – Documento que antecede a emissão da NFS-e.
- **Endpoint**: Ponto de acesso específico em uma API para realizar operações.
- **Enum**: Tipo de dado que representa um conjunto fixo de valores possíveis.
- **Erros**: Problemas críticos retornados em respostas de processamento, indicando falhas.
- **Eventos**: Registros de ocorrências relacionadas a NFS-e, como cancelamentos ou correções.
- **GZip**: Algoritmo de compressão de dados, usado para reduzir o tamanho de arquivos XML.
- **HTTP**: HyperText Transfer Protocol – Protocolo para comunicação na web.
- **HTTPS**: HTTP Secure – Versão segura do HTTP usando TLS.
- **IBGE**: Instituto Brasileiro de Geografia e Estatística – Órgão responsável por códigos de municípios e regiões.
- **ICP-Brasil**: Infraestrutura de Chaves Públicas Brasileira – Sistema nacional de certificação digital.
- **Indicador Municipal**: Marcador usado para identificar dados específicos de um município.
- **Integer**: Tipo de dado representando números inteiros.
- **ISSQN**: Imposto Sobre Serviços de Qualquer Natureza – Tributo municipal sobre serviços.
- **JSON**: JavaScript Object Notation – Formato leve para troca de dados.
- **Log**: Registro de eventos e operações para auditoria e depuração.
- **Mensagem Processamento**: Comunicação estruturada sobre o resultado de uma operação.
- **Monitoramento**: Supervisão contínua de sistemas e processos para detectar anomalias.
- **Motivo Situação Cadastral**: Razão associada ao status atual do cadastro de um contribuinte.
- **NFS-e**: Nota Fiscal de Serviços Eletrônica – Documento fiscal para serviços.
- **NSU**: Número Sequencial Único – Identificador incremental para documentos.
- **Número Sequencial de Evento**: Contador sequencial para eventos relacionados a uma NFS-e.
- **Nullable**: Indica que um campo pode ter valor nulo.
- **Object**: Estrutura de dados composta por pares chave-valor em JSON.
- **OpenAPI**: Especificação para descrever APIs REST de forma padronizada.
- **Opção Simples Nacional**: Regime tributário simplificado para micro e pequenas empresas.
- **Origem Dados**: Fonte das informações cadastrais (ex: CNC).
- **Paginação**: Técnica para dividir grandes conjuntos de dados em páginas menores.
- **Parâmetros**: Valores variáveis incluídos em mensagens de processamento.
- **Path Parameter**: Parâmetro incluído no caminho da URL de um endpoint.
- **Query Parameter**: Parâmetro incluído na query string da URL.
- **Rate Limiting**: Controle de taxa de requisições para evitar sobrecarga.
- **RFB**: Receita Federal do Brasil – Órgão federal responsável pela arrecadação tributária.
- **Regime Especial**: Tributação diferenciada aplicada a certos contribuintes ou serviços.
- **Request Body**: Corpo da requisição HTTP contendo dados enviados ao servidor.
- **Response**: Resposta retornada pelo servidor a uma requisição HTTP.
- **REST**: Representational State Transfer – Estilo arquitetural para APIs web.
- **Retenção**: Dedução de imposto na fonte pelo tomador do serviço.
- **Schema**: Estrutura ou modelo de dados definindo formato e tipos.
- **Sefin Nacional**: Secretaria da Fazenda Nacional – Órgão responsável pela administração tributária federal.
- **Situação Cadastral**: Status atual do cadastro de um contribuinte (ex: ativo, inativo).
- **Situação Emissão NFS-e**: Status de habilitação para emissão de NFS-e (ex: HABILITADO).
- **Status Code**: Código numérico indicando o resultado de uma requisição HTTP.
- **Status Processamento**: Estado geral do processamento de uma operação.
- **String**: Tipo de dado representando cadeias de caracteres.
- **Swagger**: Ferramenta para documentar e testar APIs baseadas em OpenAPI.
- **Timestamp**: Marca temporal representando data e hora.
- **Tipo de Evento**: Categoria específica de um evento relacionado a NFS-e.
- **TLS**: Transport Layer Security – Protocolo para comunicação segura na web.
- **UF**: Unidade Federativa – Estado brasileiro.
- **UTF-8**: Unicode Transformation Format 8-bit – Codificação de caracteres universal.
- **Validação**: Verificação de conformidade de dados com regras e schemas.
- **Versão Aplicativo**: Número de versão do software ou aplicação.
- **XML**: Extensible Markup Language – Linguagem para marcação de dados estruturados.
- **XMLDSIG**: XML Digital Signature – Padrão para assinatura digital de documentos XML.

Para mais detalhes, consulte os arquivos OpenAPI originais em `Api/CNC/Homologação/` e `Api/CNC/Produção/`, ou a documentação online nos URLs fornecidos em Documentação.md.
