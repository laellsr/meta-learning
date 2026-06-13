# Análise do Experimento 1: Dados Controlados (50 Datasets)

Esta análise detalha os resultados e conclusões obtidos no **Experimento 1**, que serviu como cenário controlado preliminar (prova de conceito) para validar as hipóteses do projeto de pesquisa em meta-aprendizagem.

---

## 1. Caracterização do Experimento

O experimento foi estruturado para avaliar o comportamento dos meta-modelos em um ambiente controlado e com ruído limitado.

* **Número de Datasets:** 50 datasets de classificação (4 reais da biblioteca `scikit-learn` --- Iris, Wine, Breast Cancer, Digits --- e 46 sintéticos gerados sistematicamente com `make_classification`).
* **Estrutura de Entrada ($X$):** 8 meta-features estatísticas e estruturais calculadas de forma manual (número de instâncias, número de atributos, razão $D/N$, imbalance ratio, entropia de Shannon, correlação de Pearson média, variância média e informação mútua média).
* **Algoritmos Base:** KNN, Decision Tree, RandomForest, GaussianNB, SVM (5 classificadores base).
* **Validação Cruzada:** 5 folds no nível meta.
* **Meta-modelos:** Ridge Regression, SVR, RandomForest Regressor e MLP Regressor.

---

## 2. Resultados Consolidados do Pipeline de Meta-Aprendizagem

Os resultados médios obtidos após a validação cruzada no cenário controlado são apresentados abaixo:

### Tabela de Resultados do Experimento 1 (50 Datasets)

| Meta-Modelo | Métrica | Sem Escalonamento (Baseline) | StandardScaler | MinMaxScaler | RobustScaler | QuantileTransformer |
| :--- | :--- | :---: | :---: | :---: | :---: | :---: |
| **Ridge** | MAE | 0.0832 | 0.0832 | 0.0832 | 0.0832 | **0.0667** |
| | Spearman | **0.6433** | **0.6433** | **0.6433** | **0.6433** | 0.6040 |
| | Acc@1 | **0.5000** | **0.5000** | **0.5000** | **0.5000** | 0.3400 |
| **SVR** | MAE | 0.0703 | 0.0654 | **0.0635** | 0.0640 | 0.0658 |
| | Spearman | 0.6200 | 0.6400 | 0.6240 | **0.6540** | 0.6320 |
| | Acc@1 | 0.3200 | **0.4800** | 0.4000 | **0.4800** | 0.4600 |
| **RandomForest**| MAE | **0.0623** | 0.0639 | 0.0632 | 0.0634 | 0.0630 |
| | Spearman | 0.6460 | 0.6460 | 0.6400 | 0.6360 | **0.6600** |
| | Acc@1 | **0.4600** | 0.4000 | 0.4400 | 0.4200 | **0.4600** |
| **MLPRegressor**| MAE | 0.1474 | 0.0786 | 0.1094 | **0.0710** | 0.0756 |
| | Spearman | 0.2740 | **0.5968** | 0.3969 | 0.5881 | 0.4640 |
| | Acc@1 | 0.3200 | 0.4000 | 0.2000 | **0.4400** | 0.2600 |

---

## 3. Principais Descobertas e Discussão

1. **Extrema Sensibilidade do MLP Regressor:**
   Sem o correto escalonamento do meta-alvo, o MLP colapsou, apresentando o pior MAE (**0.1474**) e uma correlação de Spearman muito baixa (**0.2740**). O uso de escalonadores baseados em centralização, especialmente o **RobustScaler**, reduziu o erro absoluto médio para **0.0710** (melhoria de **51,8%** no erro absoluto) e aumentou o ranqueamento (Acc@1 subiu de 0.32 para **0.44**).

2. **Invariância Linear do Ridge Regression:**
   Como esperado matematicamente, o Ridge apresentou desempenho idêntico sob qualquer escalonador linear (StandardScaler, MinMaxScaler e RobustScaler) e sob a Baseline. O QuantileTransformer, por introduzir não-linearidade no espaço alvo, distorceu a ordem relativa e reduziu a Acurácia de Recomendação de **0.50** para **0.34**.

3. **Robustez do Random Forest:**
   O Random Forest permaneceu praticamente invariante frente a qualquer técnica de escalonamento aplicada ao meta-alvo, com oscilação residual no MAE entre **0.0623** (Baseline) e **0.0639** (StandardScaler).

4. **SVR e o Efeito de Escalonamento Linear:**
   O SVR obteve ganhos expressivos tanto em termos de erro (reduzindo o MAE para **0.0635** com MinMaxScaler) quanto em termos de capacidade de ordenação (com a Acurácia de Recomendação Acc@1 subindo de 0.32 para **0.48** sob o StandardScaler e RobustScaler).
