# Aprendizados do Projeto Azure Databricks

## Este projeto apresenta uma visão prática do ambiente Azure utilizando uma conta gratuita de estudante. Abaixo estão os principais tópicos abordados:

🔹 Criação de Assinatura Azure: Uso de conta gratuita para ativar e configurar assinatura de estudante no portal Azure.

🔹 Criação de Grupos de Recursos: Organização lógica dos recursos do Azure para facilitar gerenciamento e controle.

🔹 Boas Práticas de Nomenclatura: Aplicação de padrões para nomes de recursos visando padronização e legibilidade.

🔹 Configuração do Azure Data Factory: Preparação do ambiente para orquestração de dados e integração com outros serviços.

🔹 Monitoramento de Uso e Custos:

Configuração de dashboards personalizados;

Uso de métricas de consumo e alertas de custo para controle financeiro.

🔹 Visualização dos Dados de Consumo: Interpretação e análise dos gastos no portal Azure.

🔹 Infraestrutura como Código:

Criação de templates com ARM Templates;

Implantação automatizada de recursos.

🔹 Automação com Azure Cloud Shell:

Utilização de comandos via terminal;

Scripts de automação para criação e gerenciamento de recursos.

# Lab de copiar uma tabela de teste de um SQL Server on-premises self-hosted para o Azure Blob Storage, usando o Azure Data Factory (ADF) por meio de um pipeline, - 30/05/2025

![image](https://github.com/user-attachments/assets/6ca04a7b-6390-4fdc-8270-03522747d636)

Conceitos:

* **Linked Service**
* **Dataset**
* **Pipeline**
* **Self-hosted Integration Runtime**
* **Medallion Architecture** (nível Bronze)

## ✅ **Objetivo:**

Mover os dados da tabela `tabela_teste` do SQL Server local para um container no Azure Blob Storage, representando a **camada Bronze** da Medallion Architecture.

## 🧰 **Tecnologias envolvidas**

* SQL Server (on-premises)
* Azure Blob Storage
* Azure Data Factory (ADF)
* Self-hosted Integration Runtime (SHIR)

## 🧱 Medallion Architecture

| Camada     | Descrição                                    |
| ---------- | -------------------------------------------- |
| **Bronze** | Dados brutos, copiados diretamente da origem |
| Silver     | Dados limpos e transformados                 |
| Gold       | Dados prontos para consumo analítico         |

Este pipeline cobre a **camada Bronze**, onde os dados são extraídos e armazenados no formato bruto (ex: CSV ou Parquet).

🔹 1. **Criar o Azure Data Factory (ADF)**

1. No portal Azure, vá em **"Create a resource" > Data Factory**.
2. Preencha os campos: nome, subscription, resource group, região, etc.
3. Crie e aguarde a implantação.

🔹 2. **Instalar e configurar o Self-hosted Integration Runtime (SHIR)**

1. No ADF, vá até **Manage > Integration Runtimes > + New**.
2. Selecione **Self-hosted** e siga o assistente.
3. Baixe o instalador e instale em uma máquina com acesso ao banco SQL.
4. Copie e cole a **chave de autenticação** gerada no ADF durante a instalação.
5. Finalize a instalação e teste a conectividade.

🔹 3. **Criar o Linked Service para o SQL Server on-premises**

1. Em **Manage > Linked services > + New**.
2. Selecione **SQL Server**.
3. Configure os seguintes campos:

   * Nome do servidor: `servidor_sql_local`
   * Nome do banco: `nome_do_banco`
   * Autenticação: SQL ou Windows
   * IR: selecione o **Self-hosted Integration Runtime**
4. Clique em **Test Connection** e depois **Create**.

### 🔹 4. **Criar o Linked Service para o Azure Blob Storage**

1. Em **Manage > Linked services > + New**.
2. Selecione **Azure Blob Storage**.
3. Configure:

   * Nome da conta de armazenamento
   * Tipo de autenticação: Chave de conta ou Managed Identity
4. Teste a conexão e salve.

🔹 5. **Criar o Dataset de origem (SQL Server)**

1. Vá para **Author > Datasets > + New Dataset**.
2. Selecione **SQL Server**.
3. Vincule ao linked service criado no passo 3.
4. Escolha a tabela `tabela_teste` ou defina uma query SQL.
5. Nomeie como `ds_sql_tabela_teste`.

### 🔹 6. **Criar o Dataset de destino (Blob Storage)**

1. Vá para **Author > Datasets > + New Dataset**.
2. Selecione **Azure Blob Storage** > formato: **DelimitedText (CSV)** ou **Parquet**.
3. Vincule ao linked service do passo 4.
4. Defina:

   * Caminho: `bronze/sql/tabela_teste/yyyy=2025/mm=05/dd=16/`
   * Nome do arquivo: `tabela_teste.csv`
5. Nomeie como `ds_blob_tabela_teste_bronze`.

🔹 7. **Criar o Pipeline de Cópia**

1. Vá para **Author > Pipelines > + New pipeline**.
2. Nomeie como `pl_copy_sql_to_blob_bronze`.
3. Arraste a atividade **Copy data** para a tela.
4. Na aba **Source**:

   * Dataset: `ds_sql_tabela_teste`
5. Na aba **Sink**:

   * Dataset: `ds_blob_tabela_teste_bronze`
   * (Opcional) Configure opções como compressão ou particionamento.
6. Salve e **Publish All**.

🔹 8. **Executar o Pipeline**

1. No topo da tela, clique em **Trigger > Trigger Now**.
2. Monitore em **Monitor** para ver status e logs.
3. Acesse o Blob Storage e verifique se o arquivo foi criado corretamente no container `bronze`.

# Explorando databricks

## 🚀 **1. Acessar a plataforma gratuita do Databricks**

1. Vá para: [https://community.cloud.databricks.com/](https://community.cloud.databricks.com/)
2. Clique em **"Sign in"** ou **"Sign up"**:

   * Caso não tenha conta, clique em **"Sign up"**, informe seus dados (email), e ative a conta pelo link enviado.

3. Após login, você verá o **Databricks Workspace** com menus laterais como **Workspace**, **Clusters**, **Jobs**, etc.

---

## ⚙️ **2. Criar um cluster (modo single-node)**

> Um cluster é necessário para executar notebooks e processar dados.

1. No menu lateral esquerdo, clique em **Compute** (ou “Clusters”).
2. Clique em **Create Cluster**.
3. Preencha os seguintes campos:

   * **Cluster name:** `single_node_cluster`
   * **Cluster mode:** Selecione **Single Node**
   * **Databricks Runtime Version:** use a versão mais recente (ex: `11.3 LTS (Scala 2.12, Spark 3.3.0)`).
   * **Node Type:** mantenha o padrão (ex: `Standard_DS3_v2`)
   * **Worker type:** não aplicável, pois é single node.
4. Clique em **Create Cluster**.
5. Aguarde o status mudar para **Running** (leva \~2 minutos).

## 📒 **3. Criar um notebook e conectar ao cluster**

1. No menu lateral, vá em **Workspace > Users > [seu\_email@databricks.com](mailto:seu_email@databricks.com)**.
2. Clique em **Create > Notebook**.
3. Preencha:

   * **Name:** `exemplo_pandas_csv`
   * **Default Language:** Python
   * **Cluster:** selecione `single_node_cluster`
4. Clique em **Create**.

## 📥 **4. Ler arquivo CSV de URL externa com Pandas**

### 📎 Fonte dos dados:

[https://raw.githubusercontent.com/MicrosoftLearning/mslearn-databricks/main/data/products.csv](https://raw.githubusercontent.com/MicrosoftLearning/mslearn-databricks/main/data/products.csv)

### ✅ Código para leitura e agregação:

```python
# 1. Importar bibliotecas
import pandas as pd

# 2. Ler o CSV da URL
url = "https://raw.githubusercontent.com/MicrosoftLearning/mslearn-databricks/main/data/products.csv"
df = pd.read_csv(url)

# 3. Visualizar as 5 primeiras linhas
display(df.head())

# 4. Agregar por ProductName e Category
agg_df = df.groupby(['ProductName', 'Category']).size().reset_index(name='Total')

# 5. Exibir resultado
display(agg_df)
```

## 🧪 Resultado

Você verá uma tabela com a contagem de produtos agrupados por `ProductName` e `Category`.

![image](https://github.com/user-attachments/assets/5672c989-2f0c-4394-bc8f-1ad8c5b2308c)


# Azure Data Fabric & Azure DevOps

# Azure Data Fabric

## 🧩 **1. O que é o Microsoft Fabric e como utilizá-lo**

O **Microsoft Fabric** é uma plataforma unificada de dados como serviço (Data as a Service - DaaS) da Microsoft que integra **engenharia de dados, ciência de dados, análise em tempo real, Power BI e governança** em uma única interface SaaS. Ele é construído sobre o Power BI e o Azure Synapse, e visa simplificar arquiteturas modernas de dados.

### ✅ **Criação de um workspace**

1. Acesse o portal: [https://app.fabric.microsoft.com/](https://app.fabric.microsoft.com/)
2. Faça login com uma conta corporativa do Microsoft 365.
3. Crie ou acesse um **workspace** (área de trabalho).
4. Dentro do workspace, você pode criar diferentes tipos de itens:

   * **Lakehouse** (para armazenar arquivos Parquet/Delta)
   * **Data Warehouse** (para modelo relacional)
   * **Notebook** (para código PySpark)
   * **Pipeline** (semelhante ao Azure Data Factory)
   * **Dataflow Gen2** (ETL visual)
   * **Reports** (com Power BI)
   * **SQL Query** (para consultas)

### 🔍 **SQL Analytics e Queries**

* Você pode usar a interface de **SQL Query Editor** dentro do **Data Warehouse** ou **Lakehouse**.
* SQL Queries no Fabric permitem consultas sobre:

  * Arquivos Delta armazenados no Lakehouse.
  * Tabelas de data warehouse com recursos como indexing e performance tuning automáticos.
* Também é possível criar **Views, Stored Procedures e Functions** diretamente no editor.

### 📈 **Analytics e Experiência Unificada**

* O Microsoft Fabric combina a experiência de Power BI com Lakehouse e Warehouse:

  * **Real-time Analytics**: com suporte a eventos e dados em tempo real.
  * **OneLake**: camada de armazenamento unificada para todos os artefatos (parquet, delta, warehouse etc).
  * **Auto Discovery**: recursos de descoberta automática de dados nos workspaces.

### 🎓 **Learning Center (Centro de Aprendizado)**

* Disponível no menu lateral esquerdo, o **Learning Center** oferece:

  * **Tutoriais guiados** sobre como usar Lakehouse, Notebooks, Pipelines e SQL queries.
  * **Laboratórios interativos** com datasets reais.
  * Acesso a **documentação oficial, vídeos e exemplos práticos**.

## 📊 **2. Monitoramento de recursos, custos e uso da calculadora**

O Microsoft Fabric adota um modelo baseado em **Capacidade de Computação (Capacity Units - CUs)**, com controle detalhado de uso e custo.

### 🔎 **Monitoramento de Recursos**

* Acesse via portal do Microsoft Fabric ou Power BI Admin Center:

  * **Usage Metrics**: horas de uso de capacidade, jobs agendados, pipelines, leitura de dados etc.
  * **Monitor Hub**: permite rastrear a execução de notebooks, pipelines e refresh de datasets.

### 💸 **Monitoramento de Custos**

* O controle de custo pode ser feito com:

  * **Azure Cost Management** (se o Fabric estiver vinculado a uma assinatura Azure).
  * **Capacity Metrics App**: app oficial que monitora uso da capacidade e projeta o custo.
  * **Power BI Admin Portal**: mostra utilização por workspace, usuário e tipo de atividade.

### 📐 **Calculadora de Preço**

* Use a calculadora oficial do Azure para estimar custos:

  * [https://azure.microsoft.com/en-us/pricing/calculator/](https://azure.microsoft.com/en-us/pricing/calculator/)
* Na calculadora:

  1. Selecione “Microsoft Fabric” ou "Power BI Premium Capacity".
  2. Configure:

     * Tipo de capacidade (F SKU, P SKU etc.)
     * Número de usuários e frequência de uso.
     * Necessidade de warehouse, pipelines, notebooks.
  3. Visualize o custo estimado mensal.

# Azure DevOps

## 🔧 1) Componentes do Azure DevOps

O Azure DevOps é um conjunto de ferramentas integradas para planejar, desenvolver, testar e entregar software com mais agilidade e controle.

### ✅ **Azure Boards**

* Ferramenta de gerenciamento ágil de projetos.
* Permite organizar **work items**, **user stories**, **bugs**, **sprints** e **backlogs**.
* Suporta **Kanban**, **Scrum** e personalização de workflows.

### 🔄 **Azure Pipelines**

* Sistema de CI/CD para compilar, testar e implantar aplicações.
* Suporta múltiplas linguagens (Python, .NET, Node.js, Java).
* Pode executar em agentes hospedados (Microsoft) ou self-hosted.
* Suporte a pipelines clássicos e YAML-as-code.
* Integração com Azure, Kubernetes, GitHub e outros.

### 🔁 **Azure Repos**

* Sistema de versionamento baseado em Git.
* Repositórios privados ilimitados.
* Suporte a **branches**, **pull requests**, **code reviews** e **merge policies**.

### 🔒 **Security & Policies**

* Gerenciamento granular de permissões por:

  * Organização
  * Projeto
  * Pipeline
  * Branch
* **Branch Policies**:

  * Pull Request obrigatória
  * Validação de build
  * Aprovação de revisores
  * Controle de quem pode escrever no `main`

### 📋 **Test Plans**

* Ferramenta para gerenciamento de testes manuais e automatizados.
* Suporta **casos de teste, testes exploratórios e execução em múltiplos ambientes**.
* Integra-se com pipelines e dashboards para rastreabilidade.

### 📦 **Artifacts**

* Sistema de gerenciamento de pacotes (NuGet, npm, Python, Maven).
* Permite armazenar e compartilhar bibliotecas internas.
* Ideal para projetos com múltiplos serviços ou times.

## 🧩 2) Extensões importantes

### 📓 **Jupyter Notebooks**

* Extensão que permite rodar, versionar e revisar **notebooks (.ipynb)** diretamente no Azure DevOps.
* Útil para projetos de ciência de dados e análises exploratórias.
* Suporte a diffs entre versões de notebook.

### 🌍 **Terraform**

* Extensão para rodar scripts de **Infraestrutura como Código (IaC)**.
* Suporta automação de provisionamento de recursos Azure.
* Comandos disponíveis via pipeline:

  * `terraform init`
  * `terraform plan`
  * `terraform apply`

---

## 🏗️ 3) Organizações e Projetos

### 🏢 **Organizações**

* Unidade raiz do Azure DevOps.
* Ex: `https://dev.azure.com/minhaempresa/`
* Uma organização pode conter múltiplos projetos.
* Controle de billing, políticas de segurança e extensões.

### 📁 **Projetos**

* Subunidade para organizar os artefatos de um time/produto.
* Cada projeto tem:

  * Repos próprios
  * Pipelines
  * Boards
  * Test Plans
  * Artifacts

## 🚀 4) Configurando Azure DevOps para um Azure Data Factory chamado **“estudo”**

### Etapas para configurar CI/CD para o ADF:

### **1. Exportar o código JSON do Data Factory**

* Acesse o portal do Azure > Data Factory > "Author".
* Use o botão **"Publish"** para consolidar alterações.
* Vá em **Manage > Source Control** e conecte com o Azure Repos.
* O código (pipelines, datasets, linked services) será exportado em formato JSON para o repositório.

### **2. Estrutura do Repositório**

```bash
/adf
  ├── datasets/
  ├── linkedServices/
  ├── pipelines/
  └── arm-template/
        ├── template.json
        └── parameters.json
```

### **3. Criar um pipeline YAML de deploy**

Arquivo: `.azure-pipelines/deploy-adf.yml`

```yaml
trigger:
  branches:
    include:
      - main

pool:
  vmImage: 'ubuntu-latest'

variables:
  azureSubscription: 'conexao-devops-azure'
  resourceGroupName: 'rg-estudo'
  dataFactoryName: 'adf-estudo'

steps:
- task: AzureResourceManagerTemplateDeployment@3
  inputs:
    deploymentScope: 'Resource Group'
    azureResourceManagerConnection: '$(azureSubscription)'
    subscriptionId: '<ID da Subscrição>'
    action: 'Create Or Update Resource Group'
    resourceGroupName: '$(resourceGroupName)'
    location: 'Brazil South'
    templateLocation: 'Linked artifact'
    csmFile: 'adf/arm-template/template.json'
    csmParametersFile: 'adf/arm-template/parameters.json'
    overrideParameters: '-factoryName $(dataFactoryName)'
    deploymentMode: 'Incremental'
```

### **4. Criar um Azure Pipeline com base no YAML**

* Acesse o projeto no Azure DevOps > Pipelines.
* Clique em **“New Pipeline”** > escolha o repositório > YAML > aponte para `deploy-adf.yml`.

### **5. Adicionar políticas e segurança**

* Vá em **Project Settings > Repositories > Branches > main**
* Adicione:

  * Políticas de Pull Request
  * Build obrigatório antes do merge
  * Revisores obrigatórios
* Em **Permissions**, controle quem pode publicar e deletar pipelines.
