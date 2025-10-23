# Sumário da Documentação Técnica - Sistema Nacional NFS-e

Este arquivo apresenta um resumo dos principais assuntos e módulos documentados no Sistema Nacional NFS-e, baseado nos arquivos de documentação técnica disponíveis.

## Visão Geral do Sistema

O Sistema Nacional NFS-e é uma plataforma integrada para gestão de Notas Fiscais de Serviços Eletrônicas (NFS-e) no Brasil. Ele inclui módulos para distribuição, recepção e consulta de documentos fiscais, com foco em contribuintes do ISSQN. Os ambientes incluem Homologação (para testes) e Produção.

### Requisitos Gerais
- Certificado Digital ICP-Brasil (e-CNPJ A1/A3).
- Autorização do Comitê Gestor da NFS-e.
- Protocolo TLS 1.2+ com autenticação mútua.
- Formatos: JSON para mensagens, XML para documentos, UTF-8, GZip + base64 para compactação.

## Módulos Principais

### 1. CNC (Cadastro Nacional de Contribuintes)
- **Função**: Gestão de dados de contribuintes, incluindo cadastros e movimentações.
- **APIs**: Distribuição, Recepção, Consulta.
- **Endpoints Principais**:
  - Distribuição: GET /cnc/municipio/cad/NSU (lotes de até 50 documentos).
  - Recepção: POST /cnc/CNC (XML compactado em base64).
  - Consulta: GET /cnc/consulta/cad (informações atuais).
- **Schemas**: DistribuicaoCNC, RecepcaoResponse, CadastroMunicipal.
- **Exemplos**: Distribuição de cadastros por NSU, consulta por município/CPF/CNPJ.

[Ver detalhes completos](CNC.md)

### 2. ADN (Ambiente de Dados Nacional)
- **Função**: Armazenamento e distribuição centralizada de documentos fiscais (DF-e) e eventos.
- **APIs**: DFe (distribuição/recepção), Eventos (vinculados a NFS-e).
- **Endpoints Principais**:
  - Distribuição DFe: GET /municipios/dfe/{NSU} (lotes até 50).
  - Recepção DFe: POST /dfe (XML compactado).
  - Eventos: GET /{ChaveAcesso}/Eventos (eventos por chave de acesso).
- **Schemas**: DistribuicaoNSU, RecepcaoResponseLote, MensagemProcessamento.
- **Exemplos**: Distribuição incremental por NSU, consulta de eventos (cancelamentos, correções).

[Ver detalhes completos](ADN.md)

### 3. DANFSe (Documento Auxiliar da NFS-e)
- **Função**: Geração e distribuição do DANFSe (versão legível da NFS-e).
- **APIs**: Consulta e geração de DANFSe.
- **Endpoints Principais**:
  - Consulta: GET /danfse (por chave de acesso ou parâmetros).
  - Geração: POST /danfse (com dados da NFS-e).
- **Schemas**: DanfseRequest, DanfseResponse, InfoDanfse.
- **Exemplos**: Geração de PDF do DANFSe, consulta por município ou período.

[Ver detalhes completos](DANFSe.md)

### 4. ParametrosMunicipais (Parâmetros Municipais)
- **Função**: Gestão de parâmetros tributários e configurações municipais para NFS-e.
- **APIs**: Consulta de alíquotas, benefícios, regimes especiais.
- **Endpoints Principais**:
  - Consulta: GET /parametros/municipais (por código IBGE, serviço, etc.).
- **Schemas**: ParametroMunicipal, Aliquota, Beneficio.
- **Exemplos**: Consulta de alíquota por município/serviço, validação de regimes especiais.

[Ver detalhes completos](ParametrosMunicipais.md)

### 5. SEFIN (Secretaria da Fazenda Nacional)
- **Função**: Integração com sistemas federais para validação e distribuição de dados fiscais.
- **APIs**: Consulta de contribuintes, validação de inscrições.
- **Endpoints Principais**:
  - Consulta: GET /sefin/contribuintes (por CPF/CNPJ).
- **Schemas**: ContribuinteSEFIN, ValidacaoResponse.
- **Exemplos**: Validação de situação cadastral, consulta de dados federais.

[Ver detalhes completos](SEFIN.md)

## Integração e Desenvolvimento

### Passos Gerais
1. Obter certificado digital válido.
2. Configurar ambiente (Homologação primeiro).
3. Preparar dados (XML assinado, compactado).
4. Fazer requisições HTTP com autenticação mútua.
5. Processar respostas JSON/XML, tratar erros/alertas.
6. Implementar paginação/NSU para distribuições.

### Tratamento de Erros
- Códigos HTTP: 200 (sucesso), 400/404 (erros).
- StatusProcessamento: PROCESSADO_COM_SUCESSO, FALHA_NO_PROCESSAMENTO, etc.
- Erros comuns: Certificado expirado (E3317), não autorizado.
- Melhores práticas: Logs, backoff exponencial, validação prévia.

### Exemplos de Código
- Python (requests): Para recepção CNC/ADN.
- Node.js (axios): Para consultas.
- Curl: Para distribuições.

## Glossário Comum
- **NSU**: Número Sequencial Único (paginação incremental).
- **Chave de Acesso**: Identificador único de 50 posições para NFS-e.
- **DFe**: Documento Fiscal Eletrônico.
- **ISSQN**: Imposto Sobre Serviços de Qualquer Natureza.
- **ICP-Brasil**: Infraestrutura de Chaves Públicas Brasileira.

Para detalhes completos, consulte os arquivos individuais: CNC.md, ADN.md, DANFSe.md, ParametrosMunicipais.md, SEFIN.md.
