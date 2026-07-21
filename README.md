# 💳 Credit Risk Lakehouse Pipeline (Databricks & PySpark)

Pipeline de Engenharia de Dados de ponta a ponta construído no **Databricks** utilizando a **Arquitetura de Medalhão** (Bronze, Silver e Gold) sobre o **Delta Lake**. O projeto realiza a ingestão, sanitização e transformação de dados de transações financeiras e histórico de crédito para suporte à tomada de decisão de risco.

---

## 🏗️ Arquitetura da Solução

O fluxo de dados foi projetado para garantir resiliência, transações ACID e alta performance de consulta:

[ Ingestão / Raw Data ]
│
▼
🥉 CAMADA BRONZE (Tabela Delta Raw: credito)
│

│  • Remoção de strings vazias e tratamento de nulos
│  • Extração de faixas salariais via RegEx
│  • Padronização cambial (USD -> BRL)
│  • Sanitização de caracteres especiais e try_cast
▼
🥈 CAMADA SILVER (Tabela Delta Limpa: silver_credito_filtrado)
│
│  • Cálculo de métricas de comprometimento financeiro
│  • Proteção contra divisão por zero (try_divide)
│  • Tipagem explícita para relatórios (DECIMAL)
│  • Otimização de layout de arquivos (OPTIMIZE)
▼
🥇 CAMADA GOLD (Tabela Analítica: gold_score_credito)


---

## 🛠️ Tecnologias Utilizadas

* **Plataforma:** Databricks (Community Edition)
* **Linguagem:** Python / PySpark
* **Armazenamento / Lakehouse:** Delta Lake
* **Processamento:** Apache Spark SQL & DataFrames API

---

## 🚀 Principais Desafios de Engenharia Resolvidos

1. **Tratamento de Dados Malformados (`CAST_INVALID_INPUT`):** Implementação de sanitização via expressões regulares (`regexp_replace`) para remoção de caracteres monetários e espaços em branco ocultos, combinados com `try_cast` para tolerar falhas de parsing sem interromper o pipeline.
2. **Parsing de Faixas Salariais em Texto:** Extração dinâmica de valores numéricos de campos com formatação de intervalos (ex: `"$60K - $80K"`) usando `regexp_extract` para cálculo de estimativa média salarial.
3. **Conversão Cambial de Precisão:** Aplicação de taxa de câmbio ($1 USD = R$ 5,50) e arredondamento financeiro padronizado via conversão explícita para o tipo `DECIMAL(18,2)`.
4. **Resiliência a Operações Matemáticas (`DIVIDE_BY_ZERO`):** Utilização do operador `try_divide` no cálculo do índice de utilização do limite de crédito para lidar com cenários de clientes com limite zerado.

---

## 📊 Regras de Negócio Implementadas (Camada Gold)

* **`salario_mensal_brl`**: Estimativa do salário mensal convertido para moeda nacional.
* **`ticket_medio_transacao_brl`**: Média gasta por transação individual no período de 12 meses.
* **`indice_uso_limite_percentual`**: Percentual do limite de crédito consumido pelo cliente em transações no ano.

---

## ⚙️ Como Rodar o Projeto

1. Clone este repositório.
2. Importe os arquivos da pasta `/notebooks` para dentro do seu espaço de trabalho no **Databricks**.
3. Faça o upload do dataset bruto para o catalogo criando a tabela `credito` (Bronze).
4. Execute as células sequencialmente a partir da camada Silver.
