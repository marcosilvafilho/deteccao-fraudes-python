# deteccao-fraudes-python
# Projeto do Curso "Analise de dados com Python: da preparação à aplicação com segurança", do bootcamp "Bradesco: GenAI, Dados e Cyber".
# Detecção de Anomalias em Transações de Cartão de Crédito

## 🎯 O Problema do Desbalanceamento

Nota-se que há um número muito pequeno de transações que são fraudulentas, em comparação com o total. Treinar um modelo com os dados sem um tratamento prévio poderá levar a erros, como ignorar operações as fraudulentas (representadas por "1" na última coluna). Neste caso, teremos um modelo que não identifica as fraudes. Precisamos utilizar métricas e tratamentos específicos. Focamos nas métricas de **Recall** (capacidade de detectar fraudes) e **Precisão**.

## 🛠️ Preparação dos Dados

Os passos abaixo foram seguidos na prepação dos dados:

* **Leitura Dinâmica:** O dataset foi carregado via URL diretamente com o Pandas, mantendo o repositório leve.
* **Privacidade:** As variáveis originais de transação (V1 a V28) já vieram anonimizadas para proteger a segurança e os dados sensíveis dos clientes.
* **Escalonamento Financeiro:** A coluna do valor das compras (`Amount`) era muito discrepante. Ela recebeu um tratamento especial para que a diferença de grandeza entre os números não "enganasse" o aprendizado de máquina.
* **Split Estratificado:** A divisão entre dados de treino e teste foi feita usando o parâmetro `stratify`, garantindo que ambos os grupos tivessem exatamente a mesma proporção de fraudes para um teste justo.
* **Táticas de Balanceamento:** Foram testadas as abordagens de *Undersampling* (corte de transações normais) e *Oversampling* usando o SMOTE (criação de fraudes sintéticas para ajudar a IA a reconhecer o perfil do ataque).



## 📊 Comparação de Modelos e Desempenho

Evolução da detecção focando na classe de fraude (Classe 1):

| Modelo | Recall | Precisão | F1-Score | Observação Tática |
| --- | --- | --- | --- | --- |
| **Regressão Logística** | 64% | 86% | 0.73 | Nosso modelo *baseline*. As curvas ROC e Precision-Recall mostraram que ele perde precisão muito rápido se tentarmos forçar a detecção de mais fraudes.
 |
| **Random Forest** | 80% | 74% | 0.77 | Usamos o parâmetro `class_weight="balanced"`, obrigando o modelo a prestar mais atenção às fraudes. O Recall deu um salto de 16%.
 |
| **XGBoost** | **78%** | **94%** | **0.85** | O modelo mais robusto. Usamos `scale_pos_weight=10` para penalizar severamente os erros contra fraudes. Entregou o melhor equilíbrio geral.
 |

> **Otimização:** Rodamos um `GridSearchCV` no modelo XGBoost para descobrir os melhores hiperparâmetros. O teste revelou que usar 100 árvores (`n_estimators=100`) com profundidade 5 (`max_depth=5`) otimizou o recall do nosso classificador.
> 
> 

## ⚙️ Limiar de Decisão e Auditoria com SHAP

* **O Limiar (Threshold):** Em um de nossos pipelines, ajustamos manualmente o limiar de decisão para **0.3** (`threshold = 0.3`). A análise gráfica confirmou que ao flexibilizar o limiar para capturar mais fraudes (maior Recall), a precisão cai, e o modelo começa a sinalizar transações legítimas como suspeitas (Falso Positivo).

* **A Caixa de Vidro (SHAP):**  Para garantir a explicabilidade, utilizamos a biblioteca **SHAP**. Ela plotou um gráfico revelando a ordem de importância das variáveis ocultas, mostrando exatamente quais fatores tiveram maior peso para a IA bloquear uma transação, uma etapa fundamental para auditorias.

## 🚀 O que mudei em relação ao projeto original da Expert

Acompanhei as instruções e passos da professora Isadora Ferrão. Em desenvolvimentos futuros, farei o empreendimento de fazer novos testes, com diferentes parâmetros e modelos. Neste curso introdutório, esforcei-me para entender a lógica e a sintaxe da programação. Também conheci importantes bibliotecas da linguagem Python para a análise e tratamento de dados.
