# Análise do Experimento com 200 Datasets Reais do OpenML (FINAL.ipynb)

Esta análise examina os resultados do experimento conduzido no notebook `FINAL.ipynb` após a otimização do cache local e da concorrência, operando com **200 datasets reais** provenientes do **OpenML**.

---

## 1. Caracterização do Novo Experimento

### 1.1 Diferenças Metodológicas em Relação aos Experimentos Anteriores

| Dimensão | Experimento 1 (REPORT.md) | Experimento 2 (ANALISYS-2.md) | Experimento 3 (Atual) |
| :--- | :--- | :--- | :--- |
| **Número de Datasets** | 50 (4 reais + 46 sintéticos) | 144 datasets reais | 200 datasets reais |
| **Fonte dos Dados** | scikit-learn + `make_classification` | OpenML | OpenML |
| **Estrutura de Cache** | Sem cache (sintéticos sob demanda) | Arquivo CSV único (420MB) | Arquivos CSV individuais na pasta `dados/datasets/` |
| **Concorrência** | Monothread | Monothread | Multithread (Produtor-Consumidor) |
| **Instâncias (N)** | $[200, 1000]$ | Mediana: 981, Máx: 2000 | Min: 21, Mediana: 512, Máx: 2000 |
| **Features (D)** | $[6, 25]$ | Mediana: 21, Máx: 1846 | Min: 2, Mediana: 17, Máx: 1841 |

---

## 2. Resultados Consolidados do Pipeline de Meta-Aprendizagem (200 Datasets)

Os resultados consolidados obtidos a partir de validação cruzada de 5 folds no nível meta estão tabulados abaixo:

### Tabela de Resultados por Meta-Modelo e Técnica de Escalonamento

| Meta-Modelo | Técnica | MAE (μ) | RMSE (μ) | Spearman (μ) | Acc@1 (μ) |
| :--- | :--- | :---: | :---: | :---: | :---: |
| **Ridge** | Baseline | 0,1469 | 0,1617 | 0,4625 | **0,4242** |
| | StandardScaler | 0,1469 | 0,1617 | 0,4625 | **0,4242** |
| | MinMaxScaler | 0,1469 | 0,1617 | 0,4625 | **0,4242** |
| | RobustScaler | 0,1469 | 0,1617 | 0,4625 | **0,4242** |
| | QuantileTransformer | 0,1451 | 0,1618 | 0,4362 | 0,3889 |
| **SVR** | Baseline | 0,1276 | 0,1431 | 0,4880 | 0,4293 |
| | StandardScaler | **0,1234** | **0,1391** | 0,5003 | 0,4293 |
| | MinMaxScaler | 0,1267 | 0,1421 | 0,4855 | 0,4293 |
| | RobustScaler | 0,1238 | 0,1395 | **0,5238** | **0,4697** |
| | QuantileTransformer | 0,1245 | 0,1407 | 0,5027 | 0,4394 |
| **RandomForest** | Baseline | 0,1148 | 0,1295 | 0,5472 | 0,5101 |
| | StandardScaler | 0,1146 | 0,1294 | 0.5475 | 0,5101 |
| | MinMaxScaler | **0,1143** | **0,1291** | **0,5543** | 0,5152 |
| | RobustScaler | 0,1144 | **0,1291** | 0,5444 | **0,5253** |
| | QuantileTransformer | 0,1193 | 0,1367 | 0,5219 | 0,4495 |
| **MLP** | Baseline | 0,1740 | 0,1999 | 0,1674 | 0,2071 |
| | StandardScaler | **0,1306** | **0,1464** | **0,4308** | 0,3737 |
| | MinMaxScaler | 0,1703 | 0,1941 | 0,2109 | 0,2020 |
| | RobustScaler | 0,1350 | 0,1509 | 0,4132 | **0,3990** |
| | QuantileTransformer | 0,1463 | 0,1651 | 0,3835 | 0,3535 |

---

## 3. Análise Estatística

### 3.1 Teste de Friedman (Significância Global)

| Meta-Modelo | χ² | p-value | Significativo? |
| :--- | :--- | :--- | :--- |
| **Ridge** | 1,6080 | 0,8074 | ✗ Não |
| **SVR** | 3,4141 | 0,4911 | ✗ Não |
| **RandomForest** | 0,3354 | 0,9874 | ✗ Não |
| **MLP** | **110,7112** | **5,13×10⁻²³** | **✓ Sim** |

### 3.2 Testes de Wilcoxon Post-Hoc (Cada Scaler vs. Baseline)

**MLP** (Friedman: extremamente significativo, $p = 5,13\times 10^{-23}$):

- **StandardScaler:** $W = 4225,00$ ($p = 3,21\times 10^{-12}$, $\Delta = -0,0434$) — **✓ Melhora Significativa**
- **MinMaxScaler:** $W = 6265,00$ ($p = 3,05\times 10^{-5}$, $\Delta = -0,0037$) — **✓ Melhora Significativa Mínima**
- **RobustScaler:** $W = 4477,00$ ($p = 2,82\times 10^{-11}$, $\Delta = -0,0390$) — **✓ Melhora Significativa**
- **QuantileTransformer:** $W = 6518,00$ ($p = 3,66\times 10^{-5}$, $\Delta = -0,0277$) — **✓ Melhora Significativa**

---

## 4. Discussão dos Resultados

1. **Confirmação do Colapso do MLP:**
   Sem o correto escalonamento do meta-alvo, o MLP colapsa obtendo Spearman de apenas **0,1674** e acurácia de recomendação marginalmente próxima do acaso (Acc@1 = **20,7%** para 5 algoritmos). O StandardScaler e o RobustScaler restauram o poder preditivo, subindo a Spearman para **0,4308** e a Acc@1 para **39,9%**.

2. **Insensibilidade e Estabilidade do RandomForest:**
   O RandomForest obteve erros e ordenação praticamente invariantes entre os escalonadores lineares, oscilando o MAE residualmente entre **0,1143** e **0,1148**, confirmando sua estabilidade a transformações de escala no meta-alvo.

3. **SVR e a Vantagem do RobustScaler no Ranking:**
   Embora o StandardScaler tenha tido o menor erro numérico absoluto (MAE = 0,1234), o **RobustScaler** sobressaiu-se fortemente na recomendação, alcançando a melhor Spearman (**0,5238**) e a melhor Acc@1 (**0,4697**).
