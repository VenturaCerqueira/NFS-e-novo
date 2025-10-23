# Documentação Técnica do DANFSe - Sistema Nacional NFS-e

Esta documentação técnica descreve os dados, esquemas, endpoints e padrões técnicos das APIs relacionadas ao Documento Auxiliar da Nota Fiscal de Serviços Eletrônica (DANFSe) do Sistema Nacional NFS-e, baseada nos arquivos OpenAPI fornecidos para os ambientes de Homologação (Produção Restrita) e Produção. Inclui informações detalhadas para desenvolvedores, como integração, exemplos de código, tratamento de erros e melhores práticas.

## Visão Geral

O DANFSe (Documento Auxiliar da Nota Fiscal de Serviços Eletrônica) é a versão legível da NFS-e, destinada aos contribuintes do ISSQN. Ele permite a geração e consulta de documentos fiscais relacionados a serviços prestados, integrando-se com o Sistema Nacional NFS-e para facilitar a emissão e gestão de notas fiscais.

### Módulos Relacionados
- **NFS-e**: Nota Fiscal de Serviços Eletrônica – Documento fiscal principal.
- **DFe**: Documentos Fiscais Eletrônicos – Distribuição de documentos.
- **Eventos**: Registros de ocorrências relacionadas a NFS-e.

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
3. **Preparar Dados**: Para geração de NFS-e, prepare a Declaração de Prestação de Serviço (DPS) válida. Para consultas, use a chave de acesso.
4. **Fazer Requisições**: Use bibliotecas HTTP que suportem certificados cliente (ex: requests em Python, axios em Node.js).
5. **Processar Respostas**: Parse JSON/XML, verifique StatusProcessamento e trate erros/alertas.
6. **Paginação/NSU**: Para distribuições, use NSU para paginação incremental.

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
- **Performance**: Processe lotes incrementalmente; evite consultas frequentes.
- **Logs**: Registre todas as requisições/respostas, incluindo chaves de acesso e timestamps.
- **Testes**: Use Homologação para validar integração antes de Produção.
- **Versionamento**: Monitore VersaoAplicativo nas respostas para compatibilidade.
- **Rate Limiting**: Respeite limites de requisições; implemente backoff exponencial em erros.
- **Validação**: Valide dados antes de envio.
- **Monitoramento**: Implemente alertas para falhas consecutivas.

## Exemplos de Código

### Python (usando requests para Consulta DANFSe)

```python
import requests

# Requisição para consultar DANFSe
url = 'https://www.producaorestrita.nfse.gov.br/danfse/12345678901234567890123456789012345678901234'
response = requests.get(url, cert=('cert.pem', 'key.pem'), verify=True)

if response.status_code == 200:
    with open('danfse.pdf', 'wb') as f:
        f.write(response.content)
    print("DANFSe baixado com sucesso.")
else:
    print(f"Erro: {response.status_code} - {response.text}")
```

### JavaScript (Node.js com axios para Geração NFS-e)

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

const url = 'https://www.producaorestrita.nfse.gov.br/nfse';
const dpsData = {
  // Dados da DPS (exemplo simplificado)
  prestador: { cnpj: '99999999999999' },
  tomador: { cpf: '99999999999' },
  servico: { valor: 100.00, descricao: 'Serviço de exemplo' }
};

axios.post(url, dpsData, { httpsAgent: agent })
  .then(response => {
    console.log('NFS-e gerada:', response.data);
  })
  .catch(error => {
    console.error('Erro:', error.response.data);
  });
```

### Curl para Consulta DANFSe

```bash
curl -X GET "https://www.producaorestrita.nfse.gov.br/danfse/12345678901234567890123456789012345678901234" \
  --cert cert.pem --key key.pem --cacert ca.pem \
  -o danfse.pdf
```

## Modelos de Dados (Schemas)

### DANFSe
Documento auxiliar da NFS-e, geralmente retornado como PDF.

- **ChaveAcesso** (string): Chave de acesso da NFS-e (50 posições).
- **Conteudo** (binary): Conteúdo do DANFSe em formato PDF.

### NFS-e
Nota Fiscal de Serviços Eletrônica.

- **ChaveAcesso** (string): Identificador único (50 posições).
- **DataEmissao** (string, date-time): Data de emissão.
- **ValorServico** (number): Valor do serviço.
- **Prestador** (object): Dados do prestador.
  - **CNPJ** (string): CNPJ do prestador.
  - **RazaoSocial** (string): Razão social.
- **Tomador** (object): Dados do tomador.
  - **CPF** (string): CPF ou CNPJ do tomador.
  - **RazaoSocial** (string): Razão social.
- **Servico** (object): Descrição do serviço.
  - **Descricao** (string): Descrição.
  - **Valor** (number): Valor.

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

#### DANFSe
- **GET /danfse/{chaveAcesso}**: Retorna o DANFSe de uma NFS-e a partir de sua chave de acesso. Resposta: PDF do documento.

#### NFS-e
- **POST /nfse**: Recepciona a DPS e Gera a NFS-e de forma síncrona. Request: Dados da DPS. Response: Dados da NFS-e gerada.
- **GET /nfse/{chaveAcesso}**: Retorna a NFS-e a partir da consulta pela chave de acesso (50 posições). Response: Dados da NFS-e.

#### Eventos
- **POST /nfse/{chaveAcesso}/eventos**: Recepciona o Pedido de Registro de Evento e gera Eventos de NFS-e, crédito, débito e apuração. Request: Dados do evento. Response: Confirmação.
- **GET /nfse/{ChaveAcesso}/Eventos**: Retorna os Documentos Fiscais de Serviço (tipo Evento) vinculados à chave de acesso. Response: Lista de eventos.
- **GET /nfse/{ChaveAcesso}/eventos/{tipoevento}**: Retorna os Documentos Fiscais de Serviço (tipo Evento) vinculados à chave de acesso e tipo de evento. Response: Lista filtrada.
- **GET /nfse/{chaveAcesso}/eventos/{tipoEvento}/{numSeqEvento}**: Retorna NFS-e e seus eventos relacionados a partir da chave de acesso, tipo e número sequencial. Response: NFS-e com eventos.

#### DFe
- **GET /DFe/{NSU}**: Distribui os DF-e para contribuintes relacionados à NFS-e. Response: Lista de DF-e.
- **GET /NFSe/{ChaveAcesso}/Eventos**: Distribui os DF-e vinculados à chave de acesso informada. Response: Lista de DF-e.

#### DPS
- **GET /dps/{id}**: Retorna a chave de acesso da NFS-e a partir do identificador do DPS. Response: Chave de acesso.
- **HEAD /dps/{id}**: Verifica se uma NFS-e foi emitida a partir do Id do DPS. Response: Status (200 se emitida).

## Exemplos

### Exemplo de Resposta de Consulta DANFSe
- Retorna um arquivo PDF com o DANFSe.

### Exemplo de Geração NFS-e (200)
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
  "valorIss": 50.00,
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
- **Base64**: Método de codificação binária para representação de dados em texto ASCII.
- **Certificado Digital**: Documento eletrônico usado para autenticação e assinatura digital, emitido por ICP-Brasil.
- **CGNFSe**: Comitê Gestor Nacional da NFS-e – Órgão responsável pela gestão e regulamentação do Sistema Nacional NFS-e.
- **Chave de Acesso**: Identificador único de 50 posições para uma NFS-e.
- **Competência**: Período fiscal ao qual se refere uma operação ou parâmetro.
- **Complemento**: Detalhes adicionais em mensagens de processamento.
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
- **Origem Dados**: Fonte das informações cadastrais (ex: DANFSe).
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

Para mais detalhes, consulte os arquivos OpenAPI originais em `Api/DANFSe/Homologação/` e `Api/DANFSe/Produção/`, ou a documentação online nos URLs fornecidos em Documentação.md.
