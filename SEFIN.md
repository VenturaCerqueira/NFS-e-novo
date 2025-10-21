# Documentação Técnica da Sefin Nacional - Sistema Nacional NFS-e

Esta documentação técnica descreve os dados, esquemas, endpoints e padrões técnicos das APIs da Secretaria da Fazenda Nacional (Sefin Nacional) do Sistema Nacional NFS-e, baseada nos arquivos OpenAPI fornecidos para os ambientes de Homologação (Produção Restrita) e Produção. Inclui informações detalhadas para desenvolvedores, como integração, exemplos de código, tratamento de erros e melhores práticas.

## Visão Geral

A Sefin Nacional é o módulo do Sistema Nacional NFS-e responsável pela administração tributária federal, focando na consulta de NFS-e e eventos relacionados. Ela permite que órgãos fiscais federais acessem informações de notas fiscais emitidas no sistema.

### Módulos Relacionados
- **NFS-e**: Nota Fiscal de Serviços Eletrônica – Documento principal consultado.
- **Eventos**: Registros de ocorrências relacionadas a NFS-e.
- **Fisco (CGNFSe, RFB, Municípios)**: Acesso para órgãos fiscais.

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
3. **Preparar Dados**: Para consultas, prepare a chave de acesso da NFS-e.
4. **Fazer Requisições**: Use bibliotecas HTTP que suportem certificados cliente (ex: requests em Python, axios em Node.js).
5. **Processar Respostas**: Parse JSON/XML, verifique StatusProcessamento e trate erros/alertas.

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
- **Performance**: Cacheie consultas frequentes para reduzir requisições.
- **Logs**: Registre todas as requisições/respostas, incluindo chaves de acesso e timestamps.
- **Testes**: Use Homologação para validar integração antes de Produção.
- **Versionamento**: Monitore VersaoAplicativo nas respostas para compatibilidade.
- **Rate Limiting**: Respeite limites de requisições; implemente backoff exponencial em erros.
- **Validação**: Valide chaves de acesso antes de consultas.
- **Monitoramento**: Implemente alertas para falhas consecutivas.

## Exemplos de Código

### Python (usando requests para Consulta NFS-e)

```python
import requests

# Requisição para consultar NFS-e
url = 'https://www.producaorestrita.nfse.gov.br/nfse/12345678901234567890123456789012345678901234'
response = requests.get(url, cert=('cert.pem', 'key.pem'), verify=True)

if response.status_code == 200:
    data = response.json()
    print(f"NFS-e: {data}")
else:
    print(f"Erro: {response.status_code} - {response.text}")
```

### JavaScript (Node.js com axios para Consulta Eventos)

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

const url = 'https://www.producaorestrita.nfse.gov.br/nfse/12345678901234567890123456789012345678901234/eventos';

axios.get(url, { httpsAgent: agent })
  .then(response => {
    console.log('Eventos:', response.data);
  })
  .catch(error => {
    console.error('Erro:', error.response.data);
  });
```

### Curl para Consulta Eventos por Tipo

```bash
curl -X GET "https://www.producaorestrita.nfse.gov.br/nfse/12345678901234567890123456789012345678901234/eventos/CANCELAMENTO" \
  --cert cert.pem --key key.pem --cacert ca.pem \
  -H "Accept: application/json"
```

## Modelos de Dados (Schemas)

### NFS-e
Nota Fiscal de Serviços Eletrônica.

- **ChaveAcesso** (string): Identificador único (50 posições).
- **Numero** (string): Número da NFS-e.
- **Serie** (string): Série.
- **DataEmissao** (string, date-time): Data de emissão.
- **Prestador** (object): Dados do prestador.
  - **CNPJ** (string): CNPJ.
  - **RazaoSocial** (string): Razão social.
- **Tomador** (object): Dados do tomador.
  - **CPF** (string): CPF ou CNPJ.
  - **RazaoSocial** (string): Razão social.
- **Servico** (object): Descrição do serviço.
  - **Descricao** (string): Descrição.
  - **Valor** (number): Valor.
  - **Aliquota** (number): Alíquota.
- **ValorISS** (number): Valor do ISS.
- **Status** (string): Status (ex: "AUTORIZADA").

### Evento
Registro de um evento relacionado a NFS-e.

- **TipoEvento** (string): Tipo (ex: "CANCELAMENTO").
- **NumSeqEvento** (integer): Número sequencial.
- **DataEvento** (string, date-time): Data do evento.
- **Justificativa** (string): Justificativa (se aplicável).

### LoteEventos
Lista de eventos.

- **Eventos** (array of Evento): Lista de eventos.

### MensagemProcessamento
Mensagem de processamento (alerta ou erro).

- **Mensagem** (object, additionalProperties: false): Mensagem genérica.
- **Parametros** (array of string, nullable): Parâmetros.
- **Codigo** (string, nullable): Código da mensagem.
- **Descricao** (string, nullable): Descrição.
- **Complemento** (string, nullable): Complemento.

## Endpoints

### APIs para o Fisco (CGNFSe, RFB, Municípios)

**Base URL Homologação**: https://www.producaorestrita.nfse.gov.br/swagger/fisco/  
**Base URL Produção**: https://www.nfse.gov.br/swagger/fisco/  

#### Sefin Nacional
- **GET /nfse/{chaveAcesso}**: Retorna a NFS-e a partir da consulta pela chave de acesso (50 posições). Response: NFS-e.
- **GET /nfse/{ChaveAcesso}/eventos**: Retorna os Documentos Fiscais de Serviço (tipo Evento) vinculados à chave de acesso. Response: LoteEventos.
- **GET /nfse/{ChaveAcesso}/eventos/{tipoevento}**: Retorna os Documentos Fiscais de Serviço (tipo Evento) vinculados à chave de acesso e tipo de evento. Response: LoteEventos filtrado.
- **GET /nfse/{chaveAcesso}/eventos/{tipoEvento}/{numSeqEvento}**: Retorna NFS-e e seus eventos relacionados a partir da chave de acesso, tipo e número sequencial. Response: NFS-e com eventos.

## Exemplos

### Exemplo de Consulta NFS-e (200)
```json
{
  "chaveAcesso": "12345678901234567890123456789012345678901234",
  "numero": "000000001",
  "serie": "1",
  "dataEmissao": "2025-10-17T10:00:00-03:00",
  "prestador": {
    "cnpj": "99999999999999",
    "razaoSocial": "Empresa Exemplo Ltda"
  },
  "tomador": {
    "cpf": "99999999999",
    "razaoSocial": "Cliente Exemplo"
  },
  "servico": {
    "descricao": "Serviço de consultoria",
    "valor": 1000.00,
    "aliquota": 0.05
  },
  "valorISS": 50.00,
  "status": "AUTORIZADA"
}
```

### Exemplo de Consulta Eventos (200)
```json
{
  "eventos": [
    {
      "tipoEvento": "CANCELAMENTO",
      "numSeqEvento": 1,
      "dataEvento": "2025-10-17T11:00:00-03:00",
      "justificativa": "Cancelamento por erro"
    }
  ]
}
```

## Glossário

- **API**: Application Programming Interface – Conjunto de regras e protocolos para interação entre aplicações de software.
- **Alertas**: Avisos não críticos retornados em respostas de processamento, indicando situações que não impedem o sucesso da operação.
- **Array**: Lista ordenada de elementos em estruturas de dados como JSON.
- **Autenticação Mútua**: Processo onde cliente e servidor se autenticam mutuamente usando certificados digitais.
- **Autoridade Certificadora**: Entidade responsável por emitir e gerenciar certificados digitais.
- **Backoff Exponencial**: Estratégia de retry onde o intervalo entre tentativas aumenta exponencialmente.
- **Certificado Digital**: Documento eletrônico usado para autenticação e assinatura digital, emitido por ICP-Brasil.
- **CGNFSe**: Comitê Gestor Nacional da NFS-e – Órgão responsável pela gestão e regulamentação do Sistema Nacional NFS-e.
- **Chave de Acesso**: Identificador único de 50 posições para uma NFS-e.
- **Complemento**: Detalhes adicionais em mensagens de processamento.
- **Código**: Identificador numérico ou alfanumérico de uma mensagem de processamento.
- **Data/Hora Processamento**: Timestamp indicando quando uma operação foi processada.
- **Descrição**: Texto explicativo de uma mensagem de processamento.
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
- **Nullable**: Indica que um campo pode ter valor nulo.
- **Número Sequencial de Evento**: Contador sequencial para eventos relacionados a uma NFS-e.
- **Object**: Estrutura de dados composta por pares chave-valor em JSON.
- **OpenAPI**: Especificação para descrever APIs REST de forma padronizada.
- **Origem Dados**: Fonte das informações cadastrais (ex: Sefin Nacional).
- **Paginação**: Técnica para dividir grandes conjuntos de dados em páginas menores.
- **Parâmetros**: Valores variáveis incluídos em mensagens de processamento.
- **Path Parameter**: Parâmetro incluído no caminho da URL de um endpoint.
- **Query Parameter**: Parâmetro incluído na query string da URL.
- **Rate Limiting**: Controle de taxa de requisições para evitar sobrecarga.
- **RFB**: Receita Federal do Brasil – Órgão federal responsável pela arrecadação tributária.
- **Request Body**: Corpo da requisição HTTP contendo dados enviados ao servidor.
- **Response**: Resposta retornada pelo servidor a uma requisição HTTP.
- **REST**: Representational State Transfer – Estilo arquitetural para APIs web.
- **Schema**: Estrutura ou modelo de dados definindo formato e tipos.
- **Sefin Nacional**: Secretaria da Fazenda Nacional – Órgão responsável pela administração tributária federal.
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

Para mais detalhes, consulte os arquivos OpenAPI originais em `Api/SEFIN/Homologação/` e `Api/SEFIN/Produção/`, ou a documentação online nos URLs fornecidos em Documentação.md.
