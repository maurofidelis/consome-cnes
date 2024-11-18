# O Projeto

O presente projeto visa orientar as áreas à como integrar ao CNES utilizando-se da [Especificação Técnica para Integração com o CNES](https://datasus.saude.gov.br/wp-content/uploads/2019/12/Especificacao-Tecnica-para-Integracao-com-o-Cadastro-Nacional-de-Estabelecimentos-de-Sa%C3%BAde.pdf). A ideia é, além de fornecer os códigos em python, demonstrar em linguagem clara e com minimo de termos técnicos possíveis, como realizar a integração e quais dados são retornados. 

---

## Sumário

- [Sobre a Integração](#sobre-a-integração)
- [Dados da Integração](#dados-da-integração)
    - [Organização dos Serviços e Operações](#organização-dos-serviços-e-operações)
    - [Documento de Especificação Simplificada para Integração com o CNES](#documento-de-especificação-simplificada-para-integração-com-o-cnes)
        - [EstabelecimentoSaudeService](#1-serviço-estabelecimentosaudeservice)
        - [VinculacaoProfissionalService](#2-serviço-vinculacaoprofissionalservice)
        - [ProfissionalSaudeService](#3-serviço-profissionalsaudeservice)
        - [LeitoService](#4-serviço-leitoservice)
        - [CnesService](#5-serviço-cnesservice)


---

## Sobre a Integração 

A integração do CNES funciona por meio do protoloca SOAP (Simple Object Access Protocol) que é um "código comum" que o sistemas e CNES utilizam para conversar. 

### Cenário Hipótetico 

Imagine que um município tem um sistema que deseja consultar as informações do CNES para criar um mapa da saúde local. O SOAP permite essa comunicação entre o sistema e o CNES, mesmo que utilizem linguagens e tecnologias diferentes. 

A integração funciona da seguinte forma: 

1. O sistema do município envia uma "pergunta" para o CNES, como: **"Quais são os postos de saúde no meu município?"**. Essa pergunta é enviada em um formato chamado XML, que ambos entendem

> O XML (eXtensible Markup Language) é como um **jeito organizado de escrever informações** para que diferentes sistemas possam entender e compartilhar esses dados de forma fácil. Pense nele como uma forma de passar mensagens com "etiquetas" que dizem exatamente o que cada informação significa.

> Exemplo de XML
```xml
<PostoDeSaude>
  <Nome>Unidade Básica de Saúde Central</Nome>
  <Endereco>Rua Principal, 123</Endereco>
  <Municipio>Belo Horizonte</Municipio>
</PostoDeSaude>
```
> No exemplo, `<PostoDeSaude>` é uma "etiqueta" que diz que o assunto é sobre um posto de saúde; `<Nome>`, `<Endereco>` e `<Municipio>` são etiquetas que dizem que daca pedaço de informação representa

2. O CNES recebe a pergunta, processa e responde no mesmo formato, com algo como: **"Aqui está a lista de postos de saúde no seu município."**

3. O sistem do município interpreta a resposta e mostra os dados para os usuários 

---

## Dados da Integração 

A documentação do DATASUS separa a integração por serviços, sendo que cada serviço possui uma série de operações que a compoe, sendo necessário (em alguns casos) o envio de alguns parâmetros que retornam dados. 

### Organização dos Serviços e Operações 

```plaintext
/soap-cnes
├── EstabelecimentoSaudeService                     # Serviço que fornece informações sobre os estabelecimentos de saúde cadastrados no CNES
│   ├── consultarEstabelecimentoSaude               # Recupera os Estabelecimentos de Saúde cadastrados no CNES
│   ├── consultarPreCadastroCNES                    # Recupera os estabelecimentos de saúde pre cadastrados no CNES 
|   ├── localizarEstabelecimentoSaude               # Possibilita a busca e apresentação de estabelecimentos de saúde próximos à localização do usuário
├── VinculacaoProfissionalService                   # Serviço que trata das informações sobre vínculos de profissional de saúde com estabelecimentos
|   ├── detalharVinculacaoProfissionalSaude         # Detalha os vínculos do profissional com estabelecimento de saúde 
|   ├── pesquisarVinculacaoProfissionalSaude        # Pesquisa informação do vínculo do profissional com estabelecimento de Saúde
├── ProfissionalSaudeService                        # Serviço que fornece informações sobre os profissionais de saúde cadastrados no CNES 
|   ├──  consultarProfissionaisSaude                # Retorna uma lista de profissionais por estabelecimento
|   ├──  consultarProfissionalSaude                 # Retorna dados de um único profissional de saúde
├── LeitoService                                    # Serviço que fornece informações sobre os leitos cadastrados nos estabelecimentos de saúde   
|   ├── consultarLeitosCnes                         # Retorna dados dos Leitos do Estabelecimento de Saúde
├── EquipamentosService                             # Serviço que retorna dados dos Equipamentos de Saúde
|   ├── consultarEquipamentos                       # Retorna dados dos equipamentos dos Estabelecimentos de Saúde
└── CNESService                                     # Serviço que centraliza informações gerais sobre os estabelecimentos de saúde cadastrado no CNES 
|   ├── consultarEstabelecimentoSaude               
|   ├── consultarEstabelecimentoSaudePorMunicipio  
    └── consultarDadosComplementaresEstabelecimentoSaude
    
```

### Documento de Especificação Simplificada para Integração com o CNES
A seguir, apresento um documento simplificado e em linguagem acessível, separando os dados de entrada e saída por serviço e operação, conforme especificado no PDF.

#### 1. **Serviço: EstabelecimentoSaudeService**
Este serviço fornece informações sobre estabelecimentos de saúde cadastrados no CNES.

##### **Operação: consultarEstabelecimentoSaude**
- **Dados de Entrada**:
  - Código CNES do estabelecimento (opcional).
  - CNPJ do estabelecimento (opcional).

- **Dados de Saída**:
  - Nome Fantasia e Nome Empresarial do estabelecimento.
  - Código CNES e CNPJ.
  - Endereço completo (logradouro, número, complemento, bairro, município, UF, CEP).
  - Tipo e esfera administrativa do estabelecimento.
  - Dados de contato (telefone, e-mail).
  - Informações sobre a localização geográfica (latitude e longitude).

---

##### **Operação: consultarPreCadastroCNES**
- **Dados de Entrada**:
  - Código CNES do pré-cadastro (opcional).
  - Situação do pré-cadastro (ATIVO, DESATIVADO ou EXCLUÍDO).

- **Dados de Saída**:
  - Nome Fantasia e Nome Empresarial.
  - Código CNES, CPF e CNPJ.
  - Natureza jurídica do estabelecimento e sua mantenedora.
  - Endereço completo.
  - Telefones e e-mails de contato.

---

##### **Operação: localizarEstabelecimentoSaude**
- **Dados de Entrada**:
  - Localização do usuário (latitude e longitude).
  - Tipo de unidade de saúde.
  - Parâmetros de paginação (número de registros por página e posição inicial).

- **Dados de Saída**:
  - Lista de estabelecimentos próximos, incluindo:
    - Nome Fantasia.
    - Endereço completo.
    - Telefone e e-mail.
    - Distância em relação à localização do usuário.

---

#### 2. **Serviço: VinculacaoProfissionalService**
Este serviço trata das informações sobre vínculos de profissionais de saúde com os estabelecimentos.

##### **Operação: detalharVinculacaoProfissionalSaude**
- **Dados de Entrada**:
  - CPF ou CNS (Cartão Nacional de Saúde) do profissional.
  - Código CNES ou CNPJ do estabelecimento.
  - Tipo de vínculo.

- **Dados de Saída**:
  - Nome e CPF do profissional.
  - Dados do estabelecimento vinculado (Nome Fantasia, Código CNES).
  - Tipo e modalidade de vínculo.
  - Carga horária do profissional no estabelecimento.

---

##### **Operação: pesquisarVinculacaoProfissionalSaude**
- **Dados de Entrada**:
  - CPF ou CNS do profissional.
  - Código CNES ou CNPJ do estabelecimento.
  - Parâmetros de paginação.

- **Dados de Saída**:
  - Lista de vínculos do profissional com os estabelecimentos, incluindo:
    - Nome do profissional.
    - Nome Fantasia do estabelecimento.
    - Tipo de vínculo.
    - Carga horária no estabelecimento.

---

#### 3. **Serviço: ProfissionalSaudeService**
Este serviço fornece informações sobre os profissionais de saúde cadastrados no CNES.

##### **Operação: consultarProfissionaisSaude**
- **Dados de Entrada**:
  - Nome do profissional (opcional).
  - CPF do profissional (opcional).
  - CNS (Cartão Nacional de Saúde) do profissional (opcional).
  - Código CNES do estabelecimento (opcional).

- **Dados de Saída**:
  - Lista de profissionais, incluindo:
    - Nome completo.
    - CPF e CNS.
    - Registro no conselho profissional (CRM, CRO, etc.).
    - Dados do estabelecimento ao qual estão vinculados (Nome Fantasia, Código CNES).

---

##### **Operação: consultarProfissionalSaude**
- **Dados de Entrada**:
  - CPF ou CNS do profissional.

- **Dados de Saída**:
  - Dados detalhados do profissional, incluindo:
    - Nome completo.
    - CPF e CNS.
    - Registro no conselho profissional (CRM, CRO, etc.).
    - Especialização e cargo.
    - Dados dos estabelecimentos onde atua:
      - Nome Fantasia.
      - Código CNES.
      - Tipo de vínculo e carga horária.

---

#### 4. **Serviço: LeitoService**
Este serviço fornece informações sobre os leitos cadastrados nos estabelecimentos de saúde.

##### **Operação: consultarLeitosCnes**
- **Dados de Entrada**:
  - Código CNES do estabelecimento.
  - Tipo de leito (opcional).

- **Dados de Saída**:
  - Código CNES do estabelecimento.
  - Tipo de leito e descrição (ex.: UTI adulto, enfermaria).
  - Quantidade de leitos disponíveis e ocupados.
  - Data da última atualização dos dados.

---

#### 5. **Serviço: CnesService**
Este serviço centraliza informações gerais sobre os estabelecimentos de saúde cadastrados no CNES.

##### **Operação: consultarEstabelecimentoSaude**
- **Dados de Entrada**:
  - Código CNES do estabelecimento (opcional).
  - CNPJ do estabelecimento (opcional).

- **Dados de Saída**:
  - Nome Fantasia e Nome Empresarial do estabelecimento.
  - Código CNES e CNPJ.
  - Endereço completo (logradouro, número, complemento, bairro, município, UF, CEP).
  - Tipo e esfera administrativa.
  - Dados de contato (telefone, e-mail).
  - Informações sobre localização geográfica (latitude e longitude).

---

##### **Operação: consultarEstabelecimentoSaudePorMunicipio**
- **Dados de Entrada**:
  - Código do município.
  - UF do município (opcional).

- **Dados de Saída**:
  - Lista de estabelecimentos do município, incluindo:
    - Nome Fantasia.
    - Código CNES e CNPJ.
    - Endereço completo.
    - Tipo e esfera administrativa.
    - Dados de contato.

---

##### **Operação: consultarDadosComplementaresEstabelecimentoSaude**
- **Dados de Entrada**:
  - Código CNES do estabelecimento.

- **Dados de Saída**:
  - Dados complementares do estabelecimento, como:
    - Especificidades de atendimento (ex.: oferta de serviços de urgência/emergência).
    - Vinculações com programas do SUS.
    - Outras informações administrativas ou técnicas do estabelecimento.


