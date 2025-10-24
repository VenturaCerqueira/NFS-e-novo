# NFS-e XML Layout Explanation

This document provides a field-by-field explanation of the NFS-e XML layout based on the provided data. Each field is described with its path, element, type, occurrence, size, description, and explanatory notes.

## 1. NFSe (Root)
- **XML Path**: -
- **Field**: NFS-e
- **Element**: Raiz
- **Type**: -
- **Occurrence**: -
- **Size**: -
- **Description**: -
- **Explanatory Notes**: -

## 2. NFSe/versao
- **XML Path**: NFSe/
- **Field**: versao
- **Element**: A
- **Type**: C
- **Occurrence**: 1-1
- **Size**: 1-4V2
- **Description**: Versão do leiaute da NFS-e.
- **Explanatory Notes**: -

## 3. NFSe/infNFSe
- **XML Path**: NFSe/
- **Field**: infNFSe
- **Element**: G
- **Type**: -
- **Occurrence**: 1-1
- **Size**: -
- **Description**: Grupo de informações da NFS-e
- **Explanatory Notes**: -

## 4. NFSe/infNFSe/id
- **XML Path**: NFSe/infNFSe/
- **Field**: id
- **Element**: ID
- **Type**: C
- **Occurrence**: 1-1
- **Size**: 53
- **Description**: Informar o identificador precedido do literal ‘ID’. A formação do identificador de 53 posições da NFS é: "NFS" + Cód.Mun. (7) + Amb.Ger. (1) + Tipo de Inscrição Federal (1) + Inscrição Federal (14 - CPF completar com 000 à esquerda) + nNFSe (13) + AnoMes Emis. da DPS (4) + Cód.Num. (9) + DV (1) Código numérico de 9 Posições numérico, aleatório, gerado automaticamente pelo sistema gerador da NFS-e.
- **Explanatory Notes**: -

## 5. NFSe/infNFSe/xLocEmi
- **XML Path**: NFSe/infNFSe/
- **Field**: xLocEmi
- **Element**: E
- **Type**: C
- **Occurrence**: 1-1
- **Size**: 150
- **Description**: Descrição do código de 7 dígitos da localidade emissora da NFS-e.
- **Explanatory Notes**: Descrição do nome do município emissor da NFS-e correspondente ao Código da Localidade de Emissão da DPS (cLocEmi).

## 6. NFSe/infNFSe/xLocPrestacao
- **XML Path**: NFSe/infNFSe/
- **Field**: xLocPrestacao
- **Element**: E
- **Type**: C
- **Occurrence**: 1-1
- **Size**: 150
- **Description**: Descrição do código de 7 dígitos referente ao local da prestação do serviço.
- **Explanatory Notes**: Descrição do nome do município emissor da NFS-e correspondente ao Código do Local da Prestação de Serviço da DPS (cLocPrestacao).

## 7. NFSe/infNFSe/nNFSe
- **XML Path**: NFSe/infNFSe/
- **Field**: nNFSe
- **Element**: E
- **Type**: N
- **Occurrence**: 1-1
- **Size**: 1-13
- **Description**: Número da NFS-e (Sequencial pelo emitente e tipo de emitente da NFS-e)
- **Explanatory Notes**: Número sequencial por tipo de emitente da NFS-e. A Sefin Nacional NFS-e irá gerar o número da NFS-e de forma sequencial por emitente. Por se tratar de um ambiente altamente transacional, a Sefin Nacional NFS-e não irá reutilizar números inutilizados durante a geração da NFS-e.

## 8. NFSe/infNFSe/cLocIncid
- **XML Path**: NFSe/infNFSe/
- **Field**: cLocIncid
- **Element**: E
- **Type**: N
- **Occurrence**: 0-1
- **Size**: 7
- **Description**: Código de 7 dígitos da localidade de incidência do ISSQN.
- **Explanatory Notes**: O Sistema Nacional NFS-e identifica a localidade de incidência do ISSQN conforme regras estabelecidas na LC 116/03. Existem exceções em que o Sistema Nacional NFS-e não identifica a localidade de incidência nem haverá destaque do ISSQN na emissão da NFS-e: 1) Em caso de imunidade não existe localidade de incidência para o ISSQN; 2) Em caso de exportação de serviço não existe localidade de incidência para o ISSQN; 3) Em caso de serviços sem a incidência de ISSQN (cTribNac igual a 990101), não existe localidade de incidência para o ISSQN; 4) Em caso de operação tributável deve existir localidade de incidência para o ISSQN. Inclusive para os serviços prestados para os subitens 03.04 e 22.01, se informado um município específico no local de prestação de serviço, conforme TAB.MUN_IBGE. As exceções para não existir município de incidência do ISSQN nas NFS-e de serviços prestados para os subitens 03.04 e 22.01, respectivamente, são definidos abaixo: a) Em caso de Locação, sublocação, arrendamento, direito de passagem ou permissão de uso, compartilhado ou não, de ferrovia, rodovia, postes, cabos, dutos e condutos de qualquer natureza (subitem 03.04 da lista de serviço do Sistema Nacional NFS-e), se o local de prestação de serviço informado na DPS corresponder à localidade geral definida para este serviço (0001000, conforme TAB.LOC.GERAL), então não é identificada a localidade de incidência para o ISSQN na NFS-e (as informações das localidades de incidência do ISSQN para o serviço 03.04 ocorrerão na solicitação de apuração enviada pelo contribuinte ao MAN); OBS: As operações de exploração de vias (ou rodovias) no campo de incidência do ISSQN (subitem 22.01 da lista de serviço do Sistema Nacional NFS-e) serão formalizadas pela "NFS-e Via", Nota Fiscal de Serviço eletrônica de Exploração de Via, que terá um layout específico a ser publicado em breve. b) Em caso de exploração de rodovia (subitem 22.01 da lista de serviço do Sistema Nacional NFS-e), se o local de prestação de serviço informado na DPS corresponder ao trecho de concessão de exploração de rodovia definida para este serviço (conforme TAB.ROD), então não é identificada a localidade de incidência para o ISSQN na NFS-e. OBS: O trecho de concessão de exploração de rodovia será previamente cadastrado pela concessionária no Sistema Nacional NFS-e e conterá a identificação dos municípios cujos territórios correspondam ao trecho de concessao de rodovia explorada (a identificação das localidades de incidência do ISSQN para o serviço 22.01 ocorrerão na solicitação de apuração enviada pelo contribuinte ao MAN);

## 9. NFSe/infNFSe/xLocIncid
- **XML Path**: NFSe/infNFSe/
- **Field**: xLocIncid
- **Element**: E
- **Type**: C
- **Occurrence**: 0-1
- **Size**: 150
- **Description**: Descrição da localidade de incidência do ISSQN.
- **Explanatory Notes**: O Sistema Nacional NFS-e identifica a localidade de incidência do ISSQN conforme regras estabelecidas na LC 116/03. Existem exceções em que o Sistema Nacional NFS-e não identifica a localidade de incidência nem haverá destaque do ISSQN na emissão da NFS-e: 1) Em caso de imunidade não existe localidade de incidência para o ISSQN; 2) Em caso de exportação de serviço não existe localidade de incidência para o ISSQN; 3) Em caso de serviços sem a incidência de ISSQN (cTribNac igual a 990101), não existe localidade de incidência para o ISSQN; 4) Em caso de operação tributável deve existir localidade de incidência para o ISSQN. Inclusive para os serviços prestados para os subitens 03.04 e 22.01, se informado um município específico no local de prestação de serviço, conforme TAB.MUN_IBGE. As exceções para não existir município de incidência do ISSQN nas NFS-e de serviços prestados para os subitens 03.04 e 22.01, respectivamente, são definidos abaixo: a) Em caso de Locação, sublocação, arrendamento, direito de passagem ou permissão de uso, compartilhado ou não, de ferrovia, rodovia, postes, cabos, dutos e condutos de qualquer natureza (subitem 03.04 da lista de serviço do Sistema Nacional NFS-e), se o local de prestação de serviço informado na DPS corresponder à localidade geral definida para este serviço (0001000, conforme TAB.LOC.GERAL), então não é identificada a localidade de incidência para o ISSQN na NFS-e (as informações das localidades de incidência do ISSQN para o serviço 03.04 ocorrerão na solicitação de apuração enviada pelo contribuinte ao MAN); OBS: As operações de exploração de vias (ou rodovias) no campo de incidência do ISSQN (subitem 22.01 da lista de serviço do Sistema Nacional NFS-e) serão formalizadas pela "NFS-e Via", Nota Fiscal de Serviço eletrônica de Exploração de Via, que terá um layout específico a ser publicado em breve. b) Em caso de exploração de rodovia (subitem 22.01 da lista de serviço do Sistema Nacional NFS-e), se o local de prestação de serviço informado na DPS corresponder ao trecho de concessão de exploração de rodovia definida para este serviço (conforme TAB.ROD), então não é identificada a localidade de incidência para o ISSQN na NFS-e. OBS: O trecho de concessão de exploração de rodovia será previamente cadastrado pela concessionária no Sistema Nacional NFS-e e conterá a identificação dos municípios cujos territórios correspondam ao trecho de concessao de rodovia explorada (a identificação das localidades de incidência do ISSQN para o serviço 22.01 ocorrerão na solicitação de apuração enviada pelo contribuinte ao MAN); OBS: As operações de exploração de vias (ou rodovias) no campo de incidência do ISSQN (subitem 22.01 da lista de serviço do Sistema Nacional NFS-e) serão formalizadas pela "NFS-e Via", Nota Fiscal de Serviço eletrônica de Exploração de Via que terá um layout específico a ser publicado em breve.

## 10. NFSe/infNFSe/xTribNac
- **XML Path**: NFSe/infNFSe/
- **Field**: xTribNac
- **Element**: E
- **Type**: C
- **Occurrence**: 1-1
- **Size**: 600
- **Description**: Descrição do código de tributação nacional do ISSQN.
- **Explanatory Notes**: A descrição do código de tributação nacional utilizada pelo Sistema Nacional NFS-e, para "traduzir" o código de serviço nacional, enviado pelo emitente na DPS, é a descrição dos subitens da lista de serviços do "Anexo III - Lista Nacional de Serviços", que consta ao final do Manual de Orientação ao Contribuinte do ISSQN para a Sefin Nacional NFS-e. A lista nacional utilizada é uma derivação direta da lista de serviços anexa à LC 116/03. A diferença entre as duas listas é que a lista nacional possui alguns subitens "desdobrados" nos mesmos grupamentos de itens. Os desdobros foram necessários para que alguns subitens do anexo à LC 116/03 fossem divididos em dois ou mais subitens, preservando a mesma lógica da lista original. A separação dos termos que compõem um subitem do anexo da lei para cada novo subitem na nova lista nacional não muda em essência a legislação vigente e permite atender tanto à legislação específica de pequenos, médios e grandes municípios que aderirem ao Sistema Nacional NFS-e.

## 11. NFSe/infNFSe/xTribMun
- **XML Path**: NFSe/infNFSe/
- **Field**: xTribMun
- **Element**: E
- **Type**: C
- **Occurrence**: 0-1
- **Size**: 600
- **Description**: Descrição do código de tributação municipal do ISSQN.
- **Explanatory Notes**: -

## 12. NFSe/infNFSe/xNBS
- **XML Path**: NFSe/infNFSe/
- **Field**: xNBS
- **Element**: E
- **Type**: C
- **Occurrence**: 0-1
- **Size**: 600
- **Description**: Descrição do código da NBS.
- **Explanatory Notes**: -

## 13. NFSe/infNFSe/verAplic
- **XML Path**: NFSe/infNFSe/
- **Field**: verAplic
- **Element**: E
- **Type**: C
- **Occurrence**: 1-1
- **Size**: 1-20
- **Description**: Versão da aplicação que gerou a NFS-e.
- **Explanatory Notes**: -

## 14. NFSe/infNFSe/ambGer
- **XML Path**: NFSe/infNFSe/
- **Field**: ambGer
- **Element**: E
- **Type**: N
- **Occurrence**: 1-1
- **Size**: 1
- **Description**: Ambiente gerador da NFS-e: 1- Sistema Próprio do Município; 2- Sefin Nacional NFS-e; NFS-e compartilhada pelo município para o ADN NFS-e sempre tem ambGer = 1. NFS-e emitidas pelo Sistema Nacional NFS-e sempre tem ambGer = 2.
- **Explanatory Notes**: -

## 15. NFSe/infNFSe/tpEmis
- **XML Path**: NFSe/infNFSe/
- **Field**: tpEmis
- **Element**: E
- **Type**: N
- **Occurrence**: 1-1
- **Size**: 1
- **Description**: Tipo de emissão da NFS-e: 1 - Emissão direta no modelo da NFS-e Nacional; 2 - Emissão original em leiaute próprio do município com transcrição para o modelo da NFS-e Nacional.
- **Explanatory Notes**: O sistema municipal pode emitir a NFS-e seguindo o XML do modelo da NFS-e Nacional ou pode manter seu próprio modelo, diferente do modelo padrão nacional. Caso mantenha seu próprio modelo, o município deverá transcrever as informações das suas NFS-e para o modelo da NFS-e nacional e assinar o documento para depois compartilhar as NFS-e transcritas para o ADN NFS-e. Neste caso, tpEmis = 2. Caso o município emita suas NFS-e já no modelo da NFS-e padrão nacional o tpEmis = 1. Notas emitidas pela Sefin Nacional NFS-e sempre tem tpEmis = 1.

## 16. NFSe/infNFSe/procEmi
- **XML Path**: NFSe/infNFSe/
- **Field**: procEmi
- **Element**: E
- **Type**: N
- **Occurrence**: 0-1
- **Size**: 1
- **Description**: Processo de Emissão da DPS: 1 - Emissão com aplicativo do contribuinte (via Web Service); 2 - Emissão com aplicativo disponibilizado pelo fisco (Web); 3 - Emissão com aplicativo disponibilizado pelo fisco (App);
- **Explanatory Notes**: Esta informação deve ser preenchida somente em NFS-e emitidas pelo Sistema Nacional NFS-e. Municipios com emissores próprios não podem informar este campo na transcrição de suas NFS-e para o compartilhamento com o ADN.

## 17. NFSe/infNFSe/cStat
- **XML Path**: NFSe/infNFSe/
- **Field**: cStat
- **Element**: E
- **Type**: N
- **Occurrence**: 1-1
- **Size**: 3
- **Description**: Situações possíveis: 100 - NFS-e Gerada; 101 - NFS-e de Substituição Gerada; 102 - NFS-e de Decisão Judicial; 103 - NFS-e Avulsa; 107 - NFS-e MEI; 108 - Nota de Crédito; 109 - Nota de Débito;
- **Explanatory Notes**: -

## 18. NFSe/infNFSe/dhProc
- **XML Path**: NFSe/infNFSe/
- **Field**: dhProc
- **Element**: E
- **Type**: D
- **Occurrence**: 1-1
- **Size**: -
- **Description**: Data/Hora da validação da DPS e geração da NFS-e. Data e hora no formato UTC (Universal Coordinated Time): AAAA-MM-DDThh:mm:ssTZD
- **Explanatory Notes**: -

## 19. NFSe/infNFSe/nDFe
- **XML Path**: NFSe/infNFSe/
- **Field**: nDFe
- **Element**: E
- **Type**: N
- **Occurrence**: 1-1
- **Size**: 1-13
- **Description**: Número sequencial do documento gerado por ambiente emissor próprio do múnicípio.
- **Explanatory Notes**: -

## 20. NFSe/infNFSe/emit
- **XML Path**: NFSe/infNFSe/
- **Field**: emit
- **Element**: G
- **Type**: G
- **Occurrence**: 1-1
- **Size**: -
- **Description**: Grupo de informações da DPS relativas ao emitente da NFS-e
- **Explanatory Notes**: -

## 21. NFSe/infNFSe/emit/CNPJ
- **XML Path**: NFSe/infNFSe/emit/
- **Field**: CNPJ
- **Element**: CE
- **Type**: N
- **Occurrence**: 1-1
- **Size**: 14
- **Description**: Número da inscrição federal (CNPJ) do emitente da NFS-e.
- **Explanatory Notes**: -

## 22. NFSe/infNFSe/emit/CPF
- **XML Path**: NFSe/infNFSe/emit/
- **Field**: CPF
- **Element**: CE
- **Type**: N
- **Occurrence**: 1-1
- **Size**: 11
- **Description**: Número da inscrição federal (CPF) do emitente da NFS-e.
- **Explanatory Notes**: -

## 23. NFSe/infNFSe/emit/IM
- **XML Path**: NFSe/infNFSe/emit/
- **Field**: IM
- **Element**: E
- **Type**: C
- **Occurrence**: 0-1
- **Size**: 15
- **Description**: Número de inscrição municipal do emitente da NFS-e.
- **Explanatory Notes**: -

## 24. NFSe/infNFSe/emit/xNome
- **XML Path**: NFSe/infNFSe/emit/
- **Field**: xNome
- **Element**: E
- **Type**: C
- **Occurrence**: 1-1
- **Size**: 150
- **Description**: Nome / Razão Social do emitente.
- **Explanatory Notes**: -

## 25. NFSe/infNFSe/emit/xFant
- **XML Path**: NFSe/infNFSe/emit/
- **Field**: xFant
- **Element**: E
- **Type**: C
- **Occurrence**: 0-1
- **Size**: 150
- **Description**: Nome / Fantasia do emitente.
- **Explanatory Notes**: -

## 26. NFSe/infNFSe/emit/enderNac
- **XML Path**: NFSe/infNFSe/emit/
- **Field**: enderNac
- **Element**: CG
- **Type**: -
- **Occurrence**: 1-1
- **Size**: -
- **Description**: Grupo de informações do endereço nacional do Emitente da NFS-e
- **Explanatory Notes**: -

## 27. NFSe/infNFSe/emit/enderNac/xLgr
- **XML Path**: NFSe/infNFSe/emit/enderNac/
- **Field**: xLgr
- **Element**: E
- **Type**: C
- **Occurrence**: 1-1
- **Size**: 1-255
- **Description**: Tipo e nome do logradouro da localização do endereço do emitente.
- **Explanatory Notes**: -

## 28. NFSe/infNFSe/emit/enderNac/nro
- **XML Path**: NFSe/infNFSe/emit/enderNac/
- **Field**: nro
- **Element**: E
- **Type**: C
- **Occurrence**: 1-1
- **Size**: 1-60
- **Description**: Número do imóvel do endereço do emitente.
- **Explanatory Notes**: -

## 29. NFSe/infNFSe/emit/enderNac/xCpl
- **XML Path**: NFSe/infNFSe/emit/enderNac/
- **Field**: xCpl
- **Element**: E
- **Type**: C
- **Occurrence**: 0-1
- **Size**: 1-156
- **Description**: Complemento do endereço do emitente.
- **Explanatory Notes**: -

## 30. NFSe/infNFSe/emit/enderNac/xBairro
- **XML Path**: NFSe/infNFSe/emit/enderNac/
- **Field**: xBairro
- **Element**: E
- **Type**: C
- **Occurrence**: 1-1
- **Size**: 1-60
- **Description**: Bairro do endereço do emitente.
- **Explanatory Notes**: -

## 31. NFSe/infNFSe/emit/enderNac/cMun
- **XML Path**: NFSe/infNFSe/emit/enderNac/
- **Field**: cMun
- **Element**: E
- **Type**: N
- **Occurrence**: 1-1
- **Size**: 7
- **Description**: Código do município do endereço do emitente. (Tabela do IBGE)
- **Explanatory Notes**: -

## 32. NFSe/infNFSe/emit/enderNac/UF
- **XML Path**: NFSe/infNFSe/emit/enderNac/
- **Field**: UF
- **Element**: E
- **Type**: C
- **Occurrence**: 1-1
- **Size**: 2
- **Description**: Sigla da unidade da federação do município do endereço do emitente.
- **Explanatory Notes**: -

## 33. NFSe/infNFSe/emit/enderNac/CEP
- **XML Path**: NFSe/infNFSe/emit/enderNac/
- **Field**: CEP
- **Element**: E
- **Type**: N
- **Occurrence**: 1-1
- **Size**: 8
- **Description**: Número do CEP do endereço do emitente. (Informar os zeros não significativos)
- **Explanatory Notes**: -

## 34. NFSe/infNFSe/emit/fone
- **XML Path**: NFSe/infNFSe/emit/
- **Field**: fone
- **Element**: E
- **Type**: N
- **Occurrence**: 0-1
- **Size**: 9-20
- **Description**: Número do telefone do emitente. (Preencher com o Código DDD + número do telefone. Nas operações com exterior é permitido informar o código do país + código da localidade + número do telefone)
- **Explanatory Notes**: -

## 35. NFSe/infNFSe/emit/email
- **XML Path**: NFSe/infNFSe/emit/
- **Field**: email
- **Element**: E
- **Type**: C
- **Occurrence**: 0-1
- **Size**: 80
- **Description**: E-mail do emitente.
- **Explanatory Notes**: -

## 36. NFSe/infNFSe/valores
- **XML Path**: NFSe/infNFSe/
- **Field**: valores
- **Element**: G
- **Type**: -
- **Occurrence**: 1-1
- **Size**: -
- **Description**: Grupo de valores referentes ao serviço prestado
- **Explanatory Notes**: -

## 37. NFSe/infNFSe/valores/issqn
- **XML Path**: NFSe/infNFSe/valores/
- **Field**: issqn
- **Element**: G
- **Type**: -
- **Occurrence**: 1-1
- **Size**: -
- **Description**: Grupo de valores para o ISSQN
- **Explanatory Notes**: -

## 38. NFSe/infNFSe/valores/issqn/vCalcDR
- **XML Path**: NFSe/infNFSe/valores/issqn/
- **Field**: vCalcDR
- **Element**: E
- **Type**: N
- **Occurrence**: 0-1
- **Size**: 1-15V2
- **Description**: Valor monetário (R$) de dedução/redução da base de cálculo (BC) do ISSQN.
- **Explanatory Notes**: vCalcDR é: o valor monetário calculado a partir do percentual de dedução/redução da BC do ISSQN, informado pelo emitente no campo pDR da DPS. Este percentual é calculado sobre valor do serviço informado na DPS e o resultado calculado é o valor deste campo do leiaute NFS-e; ou a soma dos valores de dedução/redução da BC do ISSQN, quando um ou mais documentos são informados pelo emitente na DPS. Neste caso, o resultado do somatório é o valor deste campo do leiaute NFS-e;

## 39. NFSe/infNFSe/valores/issqn/tpBM
- **XML Path**: NFSe/infNFSe/valores/issqn/
- **Field**: tpBM
- **Element**: E
- **Type**: C
- **Occurrence**: 0-1
- **Size**: 40
- **Description**: Tipo Benefício Municipal (BM): 1 ) "Isenção"; 2) "Redução da BC em 'ppBM' %"; 3) "Redução da BC em R$ 'vInfoBM' "; 4) "Alíquota Diferenciada de 'aliqDifBM' %";
- **Explanatory Notes**: Onde, nos itens abaixo: 3) ppBM é o percentual parametrizado pelo município de incidência para redução da base de cálculo do benefício municipal concedido; 4) vInfoBM é o valor informado na DPS da redução da base de cálculo do benefício municipal concedido; 5) aliqDifBM é o percentual parametrizado pelo município de incidência para alíquota diferenciada do benefício municipal concedido;

## 40. NFSe/infNFSe/valores/issqn/vCalcBM
- **XML Path**: NFSe/infNFSe/valores/issqn/
- **Field**: vCalcBM
- **Element**: E
- **Type**: N
- **Occurrence**: 0-1
- **Size**: 1-15V2
- **Description**: Valor monetário (R$) do percentual de redução da base de cálculo (BC) do ISSQN devido a um benefício municipal (BM).
- **Explanatory Notes**: Este valor é: o cálculo do valor de redução da BC do ISSQN, quando um percentual é parametrizado pelo município de incidência na lei de BM, que foi informada pelo emitente na DPS. Neste caso o percentual parametrizado é aplicado sobre o valor do serviço informado na DPS e o resultado calculado é o valor deste campo do leiaute NFS-e;

## 41. NFSe/infNFSe/valores/issqn/vBC
- **XML Path**: NFSe/infNFSe/valores/issqn/
- **Field**: vBC
- **Element**: E
- **Type**: N
- **Occurrence**: 0-1
- **Size**: 1-15V2
- **Description**: Valor da Base de Cálculo do ISSQN (R$) = Valor do Serviço - Desconto Incondicionado - Deduções/Reduções - Benefício Municipal vBC = vServ - descIncond - (vDR ou vCalcDR + vCalcReeRepRes) - (vRedBCBM ou VCalcBM)
- **Explanatory Notes**: A Base de Cálculo do ISSQN é igual a valor do serviço menos a soma dos seguintes valores: desconto incondicionado, total de deduções/reduções e benefício municipal. Sendo que: 1 - Quando o valor de dedução/redução for apurado a partir de um percentual informado na DPS, calcular este percentual sobre o valor do serviço já abatido o valor do desconto incondicionado. 2 - Quando o valor do benefício municipal for apurado a partir de um percentual parametrizado para redução da base de cálculo, aplicar o percentual parametrizado sobre o valor do serviço já abatidos os valores do desconto incondicionado e dedução/redução. OBS: As operações de exploração de vias (ou rodovias) no campo de incidência do ISSQN (subitem 22.01 da lista de serviço do Sistema Nacional NFS-e) serão formalizadas pela "NFS-e Via", Nota Fiscal de Serviço eletrônica de Exploração de Via, que terá um layout específico a ser publicado em breve. 3 - Quando houver mais de um município de incidência do ISSQN (caso do subitem 22.01.01) o valor da base de cálculo será proporcional à kilometragem do trecho da rodovia que abrange o município de incidência em função da kilometragem total da rodovia sob concessão. Este valor de trecho de rodovia é parametrizado pela concessionária no Painel Administrativo de Concessões de Rodovias e já é convertido pelo próprio painel no percentual que deve ser utilizado na multiplicação pelo valor do pedágio pago pelo motorista no guichê da praça de pedágio para resultar no valor da base de cálculo proporcional ao município de incidência em questão.

## 42. NFSe/infNFSe/valores/issqn/pAliqAplic
- **XML Path**: NFSe/infNFSe/valores/issqn/
- **Field**: pAliqAplic
- **Element**: E
- **Type**: N
- **Occurrence**: 0-1
- **Size**: 1-2V2
- **Description**: Alíquota aplicada sobre a base de cálculo para apuração do ISSQN.
- **Explanatory Notes**: A - O valor percentual da aliquota aplicada (%) poderá ser: 1) o percentual de alíquota informado pelo emitente, referente ao código de serviço, que foi informado na DPS do município de incidência do ISSQN, que identificado pelo sistema, mas que não é conveniado ao Sistema Nacional NFS-e; 2) o percentual de alíquota previamente parametrizado pelo município de incidência no código de serviço, que foi indicado pelo emitente na DPS, quando o município de incidência do ISSQN identificado pelo sistema é conveniado ao Sistema Nacional NFS-e; 3) o percentual de alíquota diferenciada, previamente parametrizada pelo município de incidência do ISSQN, no benefício municipal que foi indicado pelo emitente na DPS (quando este benefício municipal referir-se a uma alíquota diferenciada e forem satisfeitas as condições de aplicabilidade deste benefício municipal às informações prestadas pelo emitente na DPS; *A ordem de prioridade para a utilização da aliquota aplicada é decrescente conforme itens acima. Um alíquota parametrizada sobrepõem uma alíquota informada na DPS e uma alíquota diferenciada, proveniente de um benefício municipal indicado na DPS, sobrepõem uma alíquota parametrizada pelo município no código de tributação nacional ou municipal (se for o caso). *Considerar data de competência informada na DPS para recuperar a alíquota em qualquer um dos casos. B - Se o emitente informar na DPS para o campo Regime Especial de Tributação, "Profissional Autônomo" ou "Sociedade de Profissionais", e para o campo Exigibilidade, "Exigível", não há destaque de ISSQN na NFS-e. Os campos pAliqAplic, vISSQN da NFS-e não contém valor. OBS: As operações de exploração de vias (ou rodovias) no campo de incidência do ISSQN (subitem 22.01 da lista de serviço do Sistema Nacional NFS-e) serão formalizadas pela "NFS-e Via", Nota Fiscal de Serviço eletrônica de Exploração de Via, que terá um layout específico a ser publicado em breve. C - Não haverá alíquota aplicada destacada para o subitem 220101 - Serviços de exploração de rodovia mediante cobrança de preço ou pedágio dos usuários ...

## 43. NFSe/infNFSe/valores/issqn/vISSQN
- **XML Path**: NFSe/infNFSe/valores/issqn/
- **Field**: vISSQN
- **Element**: E
- **Type**: N
- **Occurrence**: 0-1
- **Size**: 1-15V2
- **Description**: Valor do ISSQN (R$) = Valor da Base de Cálculo x Alíquota vISSQN = vBC x pAliqAplic
- **Explanatory Notes**: O ISSQN não pode ser objeto de redução de base de cálculo que resulte em carga tributária menor que a decorrente da aplicação da alíquota mínima de 2,0% do valor do serviço, exceto para os serviços a que se referem os subitens 7.02, 7.05 e 16.01 da lista anexa a LC 116/03. Aguardar definição pelos municípios para implementar Não haverá ISSQN destacado para os subitens: 030401 – Locação, sublocação, arrendamento, direito de passagem ou permissão de uso, compartilhado ou não, de ferrovia, rodovia, postes, cabos, dutos e condutos de qualquer natureza. 220101 - Serviços de exploração de rodovia mediante cobrança de preço ou pedágio dos usuários, envolvendo execução de serviços de conservação, manutenção, melhoramentos para adequação de capacidade e segurança de trânsito, operação, monitoração, assistência aos usuários e outros serviços definidos em contratos, atos de concessão ou de permissão ou em normas oficiais.

## 44. NFSe/infNFSe/valores/issqn/vTotalRet
- **XML Path**: NFSe/infNFSe/valores/issqn/
- **Field**: vTotalRet
- **Element**: E
- **Type**: N
- **Occurrence**: 0-1
- **Size**: 1-15V2
- **Description**: Valor total de retenções (R$) = Σ(CP + IRRF + CSLL + ISSQN* + (PIS + CONFINS)**) vTotalRet = (vRetCP + vRetIRRF + vRetCSLL) + vISSQN* + (vPIS + vCOFINS)**
- **Explanatory Notes**: *ISSQN pode não sofrer retenção. Para o resultado do valor total de retenções o ISSQN somente será somado quando for retido. **Pis/Cofins podem não sofrer retenção. Para o resultado do valor total de retenções Pis/Cofins somente serão somados quando forem retidos.

## 45. NFSe/infNFSe/valores/issqn/vLiq
- **XML Path**: NFSe/infNFSe/valores/issqn/
- **Field**: vLiq
- **Element**: E
- **Type**: N
- **Occurrence**: 1-1
- **Size**: 1-15V2
- **Description**: Valor líquido (R$) = Valor do serviço - Desconto condicionado - Desconto incondicionado - Valores retidos (CP, IRRF, CSLL)* - Valores, se retidos (ISSQN, PIS, COFINS)** VLiq = vServ – vDescIncond – vDescCond – (vRetCP + vRetIRRF + vRetCSLL)* – (vISSQN + vPIS + vCOFINS)**
- **Explanatory Notes**: *Para o resultado do Valor Líquido o CP, IRRF e CSLL serão sempre subtraídos, se constarem na DPS, pois sempre são retidos. **Para o resultado do Valor Líquido o ISSQN, PIS e COFINS somente serão subtraídos quando forem retidos.

## 46. NFSe/infNFSe/valores/issqn/xOutInf
- **XML Path**: NFSe/infNFSe/valores/issqn/
- **Field**: xOutInf
- **Element**: E
- **Type**: C
- **Occurrence**: 0-1
- **Size**: 2000
- **Description**: Uso da Administração Tributária Municipal.
- **Explanatory Notes**: O Sistema Nacional NFS-e deverá incluir estas mensagens quando: 1) ocorrer os cenários 28 e 34, conforme a planilha "COMEX - EXPORTAÇÃO DE SERVIÇO" do AnexoI-LeiautesRN_DPS_NFSe-SNNFSe.

"Por não se tratar de um caso de Exportação de serviço ou Imunidade tributária, então, para este cenário em que foi informado que o tomador do serviço está no exterior e o serviço prestado é devido no local do estabelecimento do tomador, o sujeito passivo será o prestador do serviço e o local de incidência do ISSQN será o local da prestação do serviço, conforme os parágrafos 1º e 2º do Art. 127 do CTN."

## 47. NFSe/infNFSe/IBSCBS
- **XML Path**: NFSe
