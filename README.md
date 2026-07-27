# 📊 LinkedIn Job Scraper Automation

[![Power Automate](https://img.shields.io/badge/Power%20Automate-Low--Code-blue?logo=microsoft-power-automate)](https://powerautomate.microsoft.com/)
[![Status](https://img.shields.io/badge/Status-Production-green)]()

> **Automatize sua busca diária por vagas de Dados e BI no LinkedIn, recebendo um relatório estruturado diretamente no seu e-mail.**

---

## 📌 Sumário
- [Sobre o Projeto](#-sobre-o-projeto)
- [Funcionalidades](#-funcionalidades)
- [Arquitetura do Fluxo](#-arquitetura-do-fluxo)
- [Pré-requisitos](#-pré-requisitos)
- [Como Importar e Configurar](#-como-importar-e-configurar)
- [Variáveis de Configuração](#-variáveis-de-configuração)
- [Como Funciona (Passo a Passo)](#-como-funciona-passo-a-passo)
- [Personalização](#-personalização)
- [Estrutura dos Arquivos](#-estrutura-dos-arquivos)
- [Desafios e Soluções](#-desafios-e-soluções)
- [Melhorias Futuras](#-melhorias-futuras)
- [Contato](#-contato)

---

## 📖 Sobre o Projeto

O **LinkedIn Job Scraper Automation** é um fluxo desenvolvido no **Microsoft Power Automate** que realiza buscas diárias por vagas de **Analista de Dados, Cientista de Dados, Engenheiro de Dados e Business Intelligence (BI)** no LinkedIn, especificamente para o mercado brasileiro.

A automação:
- 🔍 **Busca** vagas publicadas nas últimas 24 horas;
- 🧹 **Extrai** e estrutura dados relevantes (título, empresa, local, modelo de trabalho, descrição e URL);
- 🎯 **Filtra** oportunidades alinhadas com o perfil desejado (excluindo cargos sênior e posições fora do Brasil);
- 📧 **Envia** um relatório diário por e-mail com uma tabela HTML profissional.

O projeto foi desenvolvido para eliminar a necessidade de buscas manuais repetitivas, economizando tempo e garantindo que você não perca nenhuma oportunidade relevante.

---

## ✨ Funcionalidades

| Funcionalidade | Descrição |
|----------------|-----------|
| **Busca Multi-palavra** | Permite buscar por múltiplos termos (ex: "Analista de Dados", "Data Scientist") em um único fluxo. |
| **Geolocalização Precisa** | Utiliza `GeoId` para garantir que os resultados sejam exclusivamente do Brasil. |
| **Filtro de Tempo** | Captura apenas vagas publicadas nas últimas 24 horas (`f_TPR=r86400`). |
| **Modelos de Trabalho** | Inclui remoto, híbrido e presencial (parâmetros 1, 2 e 3). |
| **Deduplicação** | Remove IDs duplicados automaticamente com `union()`. |
| **Parsing de HTML** | Extrai informações estruturadas a partir de HTML não estruturado. |
| **Filtro Avançado** | Seleciona vagas com base em: <br> • Título (palavras-chave como "Dados", "BI", "Data Analyst") <br> • Descrição (skills como SQL, Python, Power BI) <br> • Exclusão de termos sênior ("Sênior", "Lead", "Gerente") <br> • Exclusão de localizações nos EUA. |
| **Relatório Visual** | Gera uma tabela HTML com cores da marca LinkedIn e link direto para cada vaga. |
| **Tratamento de Erros** | Continua processando vagas mesmo se uma falhar, garantindo robustez. |
| **Agendamento Flexível** | Executado diariamente às 23h (horário de Brasília), ajustável conforme necessidade. |

---

## 🏗️ Arquitetura do Fluxo

```
    A[Gatilho Agendado - 23h] --> B[Inicialização de Variáveis]
    B --> C[Loop por Palavra-Chave]
    C --> D[Loop por Página]
    D --> E[Requisição GET - Lista de Vagas]
    E --> F[Extração de IDs]
    F --> G[Deduplicação]
    G --> H[Loop por Vaga (máx 30)]
    H --> I[Requisição GET - Detalhe da Vaga]
    I --> J[Atraso de 5s]
    J --> K[Parsing de HTML → JSON]
    K --> L[Validação e Conversão]
    L --> M[Filtro Avançado]
    M --> N[Construção Tabela HTML]
    N --> O[Envio de E-mail]
```

---

## 🛠️ Pré-requisitos

Antes de importar o fluxo, certifique-se de ter:

| Recurso | Descrição |
|---------|-----------|
| **Conta Microsoft** | Com acesso ao [Power Automate](https://powerautomate.microsoft.com/). |
| **Licença Power Automate** | Plano com suporte a conectores HTTP e agendamento (plano gratuito tem limitações). |
| **Conta Office 365** | Com Outlook para envio de e-mails (ou adapte para outro conector de e-mail). |
| **Permissões** | Para criar conexões com Office 365 e HTTP. |

---

## 📦 Como Importar e Configurar

### 1. Clone este repositório
```bash
git clone [https://github.com/Henrique08-dev/Projeto_Automacao_Vagas_LinkedIn.git]
cd linkedin-job-scraper-automation
```

### 2. Importar o fluxo no Power Automate
1. Acesse o [Power Automate Portal](https://make.powerautomate.com/).
2. Vá em **Meus fluxos** → **Importar** → **Pacote**.
3. Selecione o arquivo `.zip` contendo os arquivos JSON (disponíveis neste repositório).
4. Aguarde o upload e revise os recursos importados.
5. Conecte as referências de conexão:
   - Para **Office 365 Outlook**, selecione ou crie uma nova conexão.
   - Para **HTTP**, nenhuma conexão especial é necessária (é um conector interno).
6. Clique em **Importar**.

### 3. Ajustar parâmetros (opcional)
Edite as variáveis iniciais conforme sua preferência (veja seção abaixo).

### 4. Ativar o fluxo
Após a importação, ative o fluxo e ele começará a ser executado no horário agendado.

---

## ⚙️ Variáveis de Configuração

As principais variáveis inicializadas no fluxo são:

| Variável | Tipo | Valor Exemplo | Descrição |
|----------|------|---------------|-----------|
| `varKeywordsArray` | Array | `["Analista de Dados", "Cientista de Dados", ...]` | Palavras-chave para busca. |
| `varLocation` | String | `"Brasil"` | Localidade textual. |
| `varGeoId` | String | `"106057199"` | ID geográfico do LinkedIn (Brasil). |
| `varWorkModel` | Array | `[1, 2, 3]` | Modelos de trabalho (1=Presencial, 2=Remoto, 3=Híbrido). |
| `varTimeFilter` | String | `"r86400"` | Filtro de tempo (últimas 24h). |
| `varMaxVagas` | Integer | `30` | Máximo de vagas a capturar por execução. |
| `varPaginas` | Array | `[0, 25, 50, 75, 100]` | Paginação da lista de vagas. |
| `varTodosIDs` | Array | `[]` | (Interno) Acumula IDs das vagas. |
| `VagasProcessadas` | Array | `[]` | (Interno) Armazena vagas detalhadas. |
| `varEmailHTML` | String | `""` | (Interno) Constrói o HTML do e-mail. |

> 💡 **Dica:** Modifique `varKeywordsArray` para buscar outras áreas, como "Marketing", "Financeiro", etc.

---

## 🔄 Como Funciona (Passo a Passo)

### Fase 1 – Inicialização
- **Gatilho** agendado dispara diariamente às 23h.
- **8 variáveis** são inicializadas com os parâmetros de configuração.

### Fase 2 – Coleta de IDs
- Para **cada palavra-chave**:
  - Para **cada página** definida:
    - Faz uma requisição GET para a URL de listagem de vagas.
    - Extrai os IDs das vagas do HTML retornado.
    - Remove o primeiro item (cabeçalho) e seleciona os IDs.
    - Adiciona cada ID à matriz `varTodosIDs`.

### Fase 3 – Deduplicação
- Aplica `union()` para eliminar IDs duplicados e atualiza `varTodosIDs`.

### Fase 4 – Processamento Detalhado
- Para **cada vaga** (limitado a `varMaxVagas`):
  - Faz uma requisição GET para o detalhe da vaga.
  - Aguarda 5 segundos (para evitar bloqueio do LinkedIn).
  - Extrai: título, empresa, localização, modelo de trabalho, data de publicação e descrição.
  - Constrói um JSON estruturado.
  - Converte para objeto manipulável via Parse JSON.
  - Se bem-sucedido, adiciona à matriz `VagasProcessadas`.

### Fase 5 – Filtragem Avançada
- Aplica um **query** com múltiplos critérios:
  - **Inclusão:** Título contém palavras-chave (Dados, BI, Data Analyst, etc.) **OU** descrição contém habilidades (SQL, Python, Power BI, etc.).
  - **Exclusão:** Título não contém termos sênior ("Sênior", "Lead", "Gerente", etc.).
  - **Exclusão:** Localização não contém "United States", "Estados Unidos" ou "USA".

### Fase 6 – Geração do Relatório
- Para **cada vaga filtrada**, constrói uma linha da tabela HTML.
- Monta a tabela completa com cabeçalho estilizado.

### Fase 7 – Envio de E-mail
- Se **não houver** vagas filtradas:
  - Envia e-mail informando "Nenhuma vaga nova encontrada hoje".
- Se **houver** vagas filtradas:
  - Envia e-mail com tabela HTML contendo os detalhes e links para cada vaga.

---

## 🎨 Personalização

### Alterar Palavras-chave
Edite a variável `varKeywordsArray`:
```json
["Engenheiro de Software", "Desenvolvedor Python", "Arquiteto de Dados"]
```

### Ajustar Filtros de Habilidades
No conector **FiltroArray**, modifique a condição `contains` para adicionar ou remover habilidades:
```javascript
contains(toLower(coalesce(item()?['description'], '')), 'aws')
```

### Mudar Horário de Execução
No gatilho **Recurrence**, altere `"hours": ["23"]` para o horário desejado.

### Adaptar para Outro País
- Altere `varLocation` para o nome do país.
- Encontre o `GeoId` correto (faça uma busca manual no LinkedIn e veja o parâmetro `geoId` na URL).

### Alterar Destinatário do E-mail
Nos conectores **Enviar um e-mail (V2)**, mude o campo `"emailMessage/To"` para o e-mail desejado.

---

## 📁 Estrutura dos Arquivos

Este repositório contém os seguintes arquivos:

```
📂 Projeto_Automacao_Vagas_LinkedIn/
├── 📂 Projeto_Automação_Vagas         
├──────| 📂 Microsoft.Flow
├──────────|📂 flows
├──────────────|📄 manifest.json
├─────────────────|📂 9ad1fee6-ca02-42f1-9740-7daf41f1991b
├────────────────────|📄 apisMap.json
├────────────────────|📄 connectionsMap.json
├────────────────────|📄 definition.json       
```

### Importando no Power Automate
Para importar, **compacte todos os arquivos JSON em um único arquivo `.zip`** e faça o upload via portal do Power Automate.

> ⚠️ **Nota:** O arquivo `definition.json` contém todo o fluxo e expressões. Não o edite manualmente a menos que saiba o que está fazendo.

---

## 🧩 Desafios e Soluções

### 1. Redirecionamento para Resultados dos EUA
- **Problema:** O LinkedIn ignorava `location=Brasil` e retornava vagas dos EUA.
- **Solução:** Adicionamos o parâmetro `geoId=106057199` (ID do Brasil) à URL, forçando a geolocalização correta.

### 2. Dados Não Estruturados em HTML
- **Problema:** A página do LinkedIn não fornece dados em JSON, apenas HTML.
- **Solução:** Utilizamos expressões de manipulação de string (`split`, `substring`, `trim`) para extrair informações específicas.

### 3. Bloqueio por Múltiplas Requisições
- **Problema:** O LinkedIn pode bloquear IPs que fazem muitas requisições consecutivas.
- **Solução:** Implementamos um atraso de 5 segundos entre requisições de detalhe da vaga, evitando sobrecarga.

### 4. Robustez no Processamento
- **Problema:** Algumas vagas podem ter HTML malformado, quebrando o fluxo.
- **Solução:** Adicionamos uma condição `VerificaSucesso` que verifica se o Parse JSON foi bem-sucedido antes de adicionar a vaga ao array final.

### 5. Filtragem Complexa
- **Problema:** Necessidade de aplicar múltiplos critérios de inclusão/exclusão.
- **Solução:** Utilizamos o conector **Query** com expressões `and/or` combinando mais de 20 condições.

---

## 🚀 Melhorias Futuras

- **Integração com Azure Functions** para escalabilidade e processamento em lote.
- **Dashboard Power BI** para análise de tendências do mercado.
- **Notificações em Tempo Real** via Teams ou WhatsApp.
- **Suporte a múltiplos países** com seleção dinâmica de `GeoId`.
- **Pipeline de Dados** para armazenar histórico de vagas em um banco de dados.

## 📬 Contato

**Henrique Araujo**  
[![LinkedIn](https://img.shields.io/badge/LinkedIn-0A66C2?style=flat&logo=linkedin&logoColor=white)](https://www.linkedin.com/in/henrique-araujo-analista-de-dados/)  
[![Email](https://img.shields.io/badge/Email-henrique.araujo@maisunifacisa.com.br-blue?style=flat&logo=microsoft-outlook)](mailto:henrique.araujo@maisunifacisa.com.br)

---

> 🌟 **Se este projeto ajudou você, dê uma ⭐ no repositório e compartilhe com outros profissionais!**
