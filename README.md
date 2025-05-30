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
