# CNC XML Layout Explanation

This document provides a field-by-field explanation of the CNC (Cadastro Nacional Complementar NFS-e) XML layout based on the provided data. Each field is described with its XML path, element, type, occurrence, size, description, and explanatory notes.

## 1. CNC/versao
- **XML Path**: CNC/
- **Field**: versao
- **Element**: A
- **Type**: C
- **Occurrence**: 1-1
- **Size**: 1-4V2
- **Description**: Versão do leiaute.
- **Explanatory Notes**: -

## 2. CNC/infCNC
- **XML Path**: CNC/
- **Field**: infCNC
- **Element**: G
- **Type**: -
- **Occurrence**: 1-1
- **Size**: -
- **Description**: Grupo de informações do Cadastro Nacional Complementar NFS-e
- **Explanatory Notes**: -

## 3. CNC/infCNC/Id
- **XML Path**: CNC/infCNC/
- **Field**: Id
- **Element**: ID
- **Type**: C
- **Occurrence**: 1-1
- **Size**: 26
- **Description**: "CNC" + Cód.Mun.(7) + Tipo de Ambiente(1) + IM(15)
- **Explanatory Notes**: -

## 4. CNC/infCNC/cMun
- **XML Path**: CNC/infCNC/
- **Field**: cMun
- **Element**: E
- **Type**: N
- **Occurrence**: 1-1
- **Size**: 7
- **Description**: Código IBGE do município do contribuinte.
- **Explanatory Notes**: Conforme AnexoA-Tabelas_ISO2Paises_MunIBGE_Rodovias-SNNFSe.

## 5. CNC/infCNC/cnpjMun
- **XML Path**: CNC/infCNC/
- **Field**: cnpjMun
- **Element**: E
- **Type**: N
- **Occurrence**: 1-1
- **Size**: 14
- **Description**: CNPJ do Município.
- **Explanatory Notes**: -

## 6. CNC/infCNC/CPFAgTrib
- **XML Path**: CNC/infCNC/
- **Field**: CPFAgTrib
- **Element**: E
- **Type**: N
- **Occurrence**: 1-1
- **Size**: 11
- **Description**: CPF do agente da administração tributária municipal que enviou o registro do contribuinte do município para o CNC.
- **Explanatory Notes**: -

## 7. CNC/infCNC/tpAmb
- **XML Path**: CNC/infCNC/
- **Field**: tpAmb
- **Element**: E
- **Type**: N
- **Occurrence**: 1-1
- **Size**: 1
- **Description**: Tipo de ambiente: 1 - Produção; 2 - Homologação;
- **Explanatory Notes**: -

## 8. CNC/infCNC/verAplic
- **XML Path**: CNC/infCNC/
- **Field**: verAplic
- **Element**: E
- **Type**: C
- **Occurrence**: 1-1
- **Size**: 1-20
- **Description**: Versão do aplicativo que gerou a Movimentação.
- **Explanatory Notes**: -

## 9. CNC/infCNC/infContrib
- **XML Path**: CNC/infCNC/
- **Field**: infContrib
- **Element**: G
- **Type**: -
- **Occurrence**: 1-1
- **Size**: -
- **Description**: Grupo de informações do Contribuinte
- **Explanatory Notes**: -

## 10. CNC/infCNC/infContrib/CNPJ
- **XML Path**: CNC/infCNC/infContrib/
- **Field**: CNPJ
- **Element**: CE
- **Type**: N
- **Occurrence**: 1-1
- **Size**: 14
- **Description**: Número da inscrição federal (CNPJ) do contribuinte da NFS-e.
- **Explanatory Notes**: "Um mesmo IDFed pode ser registrado mais de uma por um mesmo município desde que cada um destes registros realizados pelo município tenham IMs diferentes entre si. Para um município, a chave para cadastro e manutenção do CNC é IDFederal (CNPJ/CPF) + Identificador Municipal (IM) (IDFed + IM são sempre obrigatórios)."

## 11. CNC/infCNC/infContrib/CPF
- **XML Path**: CNC/infCNC/infContrib/
- **Field**: CPF
- **Element**: CE
- **Type**: N
- **Occurrence**: 1-1
- **Size**: 11
- **Description**: Número da inscrição federal (CPF) do contribuinte da NFS-e.
- **Explanatory Notes**: -

## 12. CNC/infCNC/infContrib/IM
- **XML Path**: CNC/infCNC/infContrib/
- **Field**: IM
- **Element**: E
- **Type**: C
- **Occurrence**: 1-1
- **Size**: 15
- **Description**: Número do identificador municipal do contribuinte da NFS-e.
- **Explanatory Notes**: -

## 13. CNC/infCNC/infContrib/dIM
- **XML Path**: CNC/infCNC/infContrib/
- **Field**: dIM
- **Element**: E
- **Type**: D
- **Occurrence**: 1-1
- **Size**: -
- **Description**: Data do identificador municipal do contribuinte.
- **Explanatory Notes**: -

## 14. CNC/infCNC/infContrib/xFantasia
- **XML Path**: CNC/infCNC/infContrib/
- **Field**: xFantasia
- **Element**: E
- **Type**: C
- **Occurrence**: 0-1
- **Size**: 150
- **Description**: Nome fantasia do contribuinte.
- **Explanatory Notes**: -

## 15. CNC/infCNC/infContrib/ender
- **XML Path**: CNC/infCNC/infContrib/
- **Field**: ender
- **Element**: G
- **Type**: -
- **Occurrence**: 0-1
- **Size**: -
- **Description**: Grupo de informações do endereço do contribuinte do município
- **Explanatory Notes**: -

## 16. CNC/infCNC/infContrib/ender/CEP
- **XML Path**: CNC/infCNC/infContrib/ender/
- **Field**: CEP
- **Element**: E
- **Type**: N
- **Occurrence**: 1-1
- **Size**: 8
- **Description**: "Número do CEP do endereço do contribuinte. (Informar os zeros não significativos)"
- **Explanatory Notes**: -

## 17. CNC/infCNC/infContrib/ender/xLgr
- **XML Path**: CNC/infCNC/infContrib/ender/
- **Field**: xLgr
- **Element**: E
- **Type**: C
- **Occurrence**: 1-1
- **Size**: 1-255
- **Description**: Tipo e nome do logradouro da localização do endereço do contribuinte.
- **Explanatory Notes**: -

## 18. CNC/infCNC/infContrib/ender/nro
- **XML Path**: CNC/infCNC/infContrib/ender/
- **Field**: nro
- **Element**: E
- **Type**: C
- **Occurrence**: 1-1
- **Size**: 1-60
- **Description**: Número do imóvel do endereço do contribuinte.
- **Explanatory Notes**: -

## 19. CNC/infCNC/infContrib/ender/xCpl
- **XML Path**: CNC/infCNC/infContrib/ender/
- **Field**: xCpl
- **Element**: E
- **Type**: C
- **Occurrence**: 0-1
- **Size**: 1-156
- **Description**: Complemento do endereço do contribuinte.
- **Explanatory Notes**: -

## 20. CNC/infCNC/infContrib/ender/xBairro
- **XML Path**: CNC/infCNC/infContrib/ender/
- **Field**: xBairro
- **Element**: E
- **Type**: C
- **Occurrence**: 1-1
- **Size**: 1-60
- **Description**: Bairro do endereço do contribuinte.
- **Explanatory Notes**: -

## 21. CNC/infCNC/infContrib/fone
- **XML Path**: CNC/infCNC/infContrib/
- **Field**: fone
- **Element**: E
- **Type**: N
- **Occurrence**: 0-1
- **Size**: 9-20
- **Description**: "Número do telefone do contribuinte. (Preencher com o Código DDD + número do telefone. Nas operações com exterior é permitido informar o código do país + código da localidade + número do telefone)"
- **Explanatory Notes**: -

## 22. CNC/infCNC/infContrib/email
- **XML Path**: CNC/infCNC/infContrib/
- **Field**: email
- **Element**: E
- **Type**: C
- **Occurrence**: 0-1
- **Size**: 80
- **Description**: E-mail do contribuinte.
- **Explanatory Notes**: -

## 23. CNC/infCNC/infContrib/dAutEmiss
- **XML Path**: CNC/infCNC/infContrib/
- **Field**: dAutEmiss
- **Element**: E
- **Type**: D
- **Occurrence**: 1-1
- **Size**: -
- **Description**: Data inicial de autorização do contribuinte para emissão de NFS-e nos emissores públicos do Sistema Nacional NFS-e.
- **Explanatory Notes**: "Por padrão todos os contribuintes com endereço em um município pelos cadastros RFB (CNPJ e CPF) estão habilitados a emitirem NFS-e pelos Emissores Públicos Nacionais. Para desabilitar um contribuinte a emitir NFS-e pelo município, este deve registra o complemento no CNC (IDFed + IM + cStatEmiss = 0)."

## 24. CNC/infCNC/infContrib/cStatEmiss
- **XML Path**: CNC/infCNC/infContrib/
- **Field**: cStatEmiss
- **Element**: E
- **Type**: N
- **Occurrence**: 1-1
- **Size**: 1
- **Description**: "Status para emissão de NFS-e: 0 - Suspenso; 1 - Autorizado;"
- **Explanatory Notes**: "Por padrão todos os contribuintes com endereço em um município pelos cadastros RFB (CNPJ e CPF) estão habilitados a emitirem NFS-e pelos Emissores Públicos Nacionais. Para desabilitar um contribuinte a emitir NFS-e pelo município, este deve registra o complemento no CNC (IDFed + IM + cStatEmiss = 0)."

## 25. CNC/infCNC/infContrib/cSitCNC
- **XML Path**: CNC/infCNC/infContrib/
- **Field**: cSitCNC
- **Element**: E
- **Type**: N
- **Occurrence**: 1-1
- **Size**: 1
- **Description**: "Situação do registro no CNC NFS-e: 0 - Inativo; 1 - Ativo;"
- **Explanatory Notes**: Exclusão lógica do registro no CNC.

## 26. CNC/infCNC/infContrib/xSitCadMun
- **XML Path**: CNC/infCNC/infContrib/
- **Field**: xSitCadMun
- **Element**: E
- **Type**: C
- **Occurrence**: 0-1
- **Size**: 1-150
- **Description**: Descrição Situação Cadastral no Município.
- **Explanatory Notes**: -

## 27. CNC/infCNC/infContrib/xMotivoSitCadMun
- **XML Path**: CNC/infCNC/infContrib/
- **Field**: xMotivoSitCadMun
- **Element**: E
- **Type**: C
- **Occurrence**: 0-1
- **Size**: 1-255
- **Description**: Motivo da Situação Cadastral no Município.
- **Explanatory Notes**: -
