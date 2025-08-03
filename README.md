# Projeto Medallion com Microsoft Fabric: Cotações do Banco Central do Brasil

Este projeto demonstra a construção de uma arquitetura de dados completa, do zero, utilizando o **Microsoft Fabric**. O objetivo é ingerir dados de cotações diárias de moedas de uma API pública do Banco Central do Brasil (BCB), processá-los em uma estrutura **Medallion** e disponibilizá-los para análise em um relatório do Power BI.

O fluxo de trabalho é orquestrado por um **Data Pipeline**, garantindo que os dados sejam atualizados de forma automática e incremental.

---

### 🏛️ Estrutura Medallion
O projeto segue a arquitetura de dados **Medallion**, que organiza os dados em três camadas:

* **Bronze:** Camada de ingestão. Recebe os dados brutos, sem nenhuma transformação, diretamente da fonte. Os dados são salvos em arquivos **Parquet**.
* **Silver:** Camada de limpeza e tratamento. Os dados da camada Bronze são lidos, limpos e transformados. Dados são carregados em tabelas **Delta** particionadas.
* **Gold:** Camada de modelagem e agregação. Os dados da camada Silver são enriquecidos, preenchendo lacunas de datas. O resultado é um modelo dimensional pronto para consumo de relatórios e análises.

<img width="1676" height="595" alt="image" src="https://github.com/user-attachments/assets/c4380cbe-4c5a-4a2e-a95a-6ac3bb212227" />

<img width="1033" height="777" alt="image" src="https://github.com/user-attachments/assets/978c117b-9a96-4b22-b4f6-91dd1cc53c71" />




---

### 🛠️ Tecnologias Utilizadas
* **Microsoft Fabric:** Ambiente de desenvolvimento e execução.
* **Lakehouse:** Estrutura para armazenamento dos dados em cada camada (Bronze, Silver, Gold).
* **Notebooks Spark:** Utilizados para ingestão, transformação e modelagem de dados, com código **Python** e **Spark SQL**.
* **Data Pipeline:** Orquestração do fluxo de trabalho, garantindo a execução sequencial dos notebooks.
* **API do Banco Central do Brasil:** Fonte de dados para as cotações de moedas.
* **Power BI:** Criação do modelo semântico (**Direct Lake**) e do relatório final.

---

### 🗺️ Arquitetura do Projeto
O projeto segue o seguinte fluxo de dados:

1.  **Ingestão (Notebook `Ingestao_Cotacoes`):**
    * Coleta cotações diárias de várias moedas da API do BCB.
    * Itera sobre todas as moedas e utiliza paginação para obter todos os registros históricos.
    * Salva os dados brutos, com uma coluna adicional para identificar a moeda, em arquivos Parquet no `LakeHouse_Bronze`.

2.  **Processamento Bronze -> Silver (Notebook `Bronze_to_Silver`):**
    * Lê os arquivos Parquet da camada Bronze.
    * Faz a limpeza dos dados (tratamento de datas e tipos de dados).
    * Carrega os dados em uma tabela Delta particionada por moeda no `LakeHouse_Silver`.
    * Move os arquivos Parquet já processados para uma pasta de "carregados".

3.  **Processamento Silver -> Gold (Notebook `Silver_to_Gold`):**
    * Lê a tabela da camada Silver e a tabela de moedas da Bronze (via atalhos).
    * Preenche lacunas de datas com a última cotação disponível, garantindo dados contínuos para o relatório.
    * Cria e salva a tabela dimensional `Fact_Cotacoes` no `LakeHouse_Gold`.

4.  **Visualização:**
    * Um **modelo semântico** no modo **Direct Lake** é criado, conectando-se diretamente à tabela `Fact_Cotacoes`.
    * Um **relatório do Power BI** consome este modelo semântico para visualização e análise.

5.  **Orquestração:**
    * Um **Data Pipeline** é configurado para executar os três notebooks em sequência.
    * O pipeline é **agendado** para rodar diariamente, garantindo que o relatório esteja sempre atualizado sem intervenção manual.


---

### ✒️ Autor
Alexon Peot
