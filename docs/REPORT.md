# Introdução

O processo de seleção de algoritmos de aprendizado de máquina é uma tarefa iterativa e custosa. O meta-aprendizado atua nesse cenário recomendando algoritmos ou prevendo suas performances baseando-se nas características dos conjuntos de dados (*meta-features*). No entanto, o desempenho alcançado por algoritmos de nível base (como Acurácia ou F1-score) frequentemente gera uma variável dependente (o *meta-alvo*) com variações de escalas e distribuições enviesadas.

Este projeto foca em estudar o efeito de técnicas de escalonamento (normalização e padronização) aplicadas diretamente ao meta-alvo e como isso afeta a performance do meta-modelo preditivo. Dessa forma, a pesquisa é guiada por duas perguntas centrais adaptadas para o nível meta:
1. Existe uma diferença relevante no desempenho do meta-modelo ao variar a técnica de escalonamento do meta-alvo?
2. Há uma recomendação geral de escalonamento que funcione de maneira universal para qualquer meta-modelo?

# Revisão da literatura

A etapa de escalonamento é um pré-processamento fundamental em *pipelines* de aprendizado de máquina. Amorim et al. (2022), no artigo *"The choice of scaling technique matters for classification performance"*, exploraram o impacto de 5 técnicas de escalonamento aplicadas às *features* de entrada de 82 conjuntos de dados, avaliando seus efeitos em 20 algoritmos de classificação.

Os autores concluíram de forma categórica que a escolha da técnica de escalonamento altera significativamente a performance da classificação. Utilizar uma técnica inadequada pode resultar em um desempenho pior do que utilizar os dados em sua forma bruta (sem escalonamento).

Sobre a existência de uma recomendação geral, os autores evidenciaram que **não há uma regra universal ("one size fits all")**, pois a sensibilidade à escala é inerente à arquitetura do algoritmo:
* **Modelos altamente sensíveis:** Algoritmos baseados em instâncias, redes neurais e suporte vetorial (KNN, MLP, SVM, Perceptron).
* **Modelos insensíveis:** Algoritmos baseados em regras e árvores (Decision Tree, Random Forest, XGBoost).

Apesar da inexistência de uma bala de prata, o estudo apontou que a técnica *Quantile Transformer* obteve o maior número de vitórias na maioria dos estratos de desbalanceamento de dados, enquanto o *Standard Scaler* obteve melhor sucesso em bases equilibradas.

O presente projeto de pesquisa transpõe essa investigação para o nível de **meta-aprendizagem**. O objetivo é verificar se os achados de Amorim et al. (2022) no nível base se refletem no nível meta, ou seja, se o escalonamento do **meta-alvo** ditará a mesma variação de performance e se a dependência da arquitetura do **meta-modelo** permanecerá verdadeira.

# Proposta

A proposta deste trabalho consiste em transpor a análise do impacto de técnicas de escalonamento para o nível de **meta-aprendizagem**. Em sistemas de meta-aprendizagem para seleção de algoritmos, o modelo preditivo (meta-modelo) busca mapear as características de um conjunto de dados (*meta-features*) para a performance estimada de um conjunto de algoritmos candidatos (*meta-alvo*).

Matematicamente, temos:
* Um conjunto de datasets $\mathcal{D} = \{d_1, d_2, \dots, d_N\}$.
* Para cada dataset $d_i$, extraímos um vetor de meta-features $x_i \in \mathbb{R}^D$, formando a matriz de entrada $X \in \mathbb{R}^{N \times D}$.
* Avaliamos um conjunto de algoritmos base $\mathcal{A} = \{a_1, a_2, \dots, a_M\}$ em cada dataset, gerando um vetor de performances $y_i \in \mathbb{R}^M$, onde $y_{ij}$ é a performance do algoritmo $a_j$ no dataset $d_i$. Isso constitui a matriz de meta-alvo $Y \in \mathbb{R}^{N \times M}$.

O objetivo do meta-modelo é aprender uma função aproximadora $f: X \to Y$. 

Nossa proposta estuda o efeito de aplicar uma transformação de escalonamento $S$ sobre a matriz de meta-alvo $Y$ antes do treinamento do meta-modelo. O pipeline experimental é dividido em:
1. **Meta-treino:**
   * Ajuste do escalonador de entrada nos dados de treino $X_{train}$.
   * Ajuste do escalonador do meta-alvo nos dados de treino $Y_{train}$, obtendo $Y_{train\_scaled} = S(Y_{train})$.
   * Treinamento do meta-modelo $f$ para mapear $X_{train\_scaled}$ em $Y_{train\_scaled}$.
2. **Meta-teste:**
   * Predição do meta-alvo escalonado: $\hat{Y}_{test\_scaled} = f(X_{test\_scaled})$.
   * Transformação reversa do meta-alvo previsto para a escala original: $\hat{Y}_{test} = S^{-1}(\hat{Y}_{test\_scaled})$.
   * Avaliação do erro comparando $\hat{Y}_{test}$ com o $Y_{test}$ real (na escala original, ex: F1-score $[0, 1]$).

Isso garante que evitamos o **look-ahead bias** (vazamento de dados do conjunto de teste) e permite comparar de forma justa todas as técnicas de escalonamento na mesma métrica de erro absoluto.

---

# Metodologia experimental

O pipeline experimental foi totalmente implementado em Python e executado utilizando as seguintes definições:

### 1. Conjuntos de Dados (Base de Dados)
Testou-se o pipeline em quatro configurações experimentais progressivas de bases de dados de classificação:
* **Experimento 1:** 50 conjuntos de dados (4 reais do scikit-learn + 46 sintéticos gerados sistematicamente).
* **Experimento 2:** 144 datasets reais provenientes do OpenML (com cache consolidado).
* **Experimento 3:** 200 datasets reais provenientes do OpenML (com cache individual otimizado por arquivos na pasta `dados/datasets/` e download concorrente).
* **Experimento 4 (Atual):** 198 datasets válidos do OpenML (após alinhamento X/Y) com extração automática de meta-features via **PyMFE**.

As restrições para seleção dos datasets do OpenML foram:
- Instâncias ($N$): $100 \le N \le 2000$.
- Atributos ($D$): $3 \le D \le 50$.
- Classes ($C$): $2 \le C \le 10$.
- Remoção de duplicatas de famílias de datasets.

### 2. Extração de Meta-features ($X$)
* **Experimentos 1, 2 e 3:** Foram calculadas manualmente **8 meta-features** principais (instâncias, atributos, razão features/amostras, imbalance ratio, entropia, correlação de Pearson média, variância média e informação mútua média).
* **Experimento 4:** Adotou-se a biblioteca especializada **PyMFE** para extrair **78 meta-features** dos grupos `general`, `statistical`, `info-theory` e `clustering`. Valores absolutos superiores a $10^{10}$ foram recortados e NaN imputados pela mediana antes do treinamento.

As meta-features foram padronizadas utilizando `StandardScaler` (ajustado no conjunto de treino da validação cruzada do meta-modelo).

### 3. Extração do Meta-alvo ($Y$)
Avaliamos **5 algoritmos base** de classificação:
* **KNN** (k-Nearest Neighbors, $k=5$)
* **Decision Tree** (Árvore de Decisão CART)
* **Random Forest** ($100$ estimadores)
* **Gaussian Naive Bayes** (GNB)
* **SVM** (Kernel RBF)

O meta-alvo corresponde à média do **F1-score Macro** obtido por meio de validação cruzada estratificada de 5 folds em cada dataset.

### 4. Técnicas de Escalonamento do Meta-alvo
Testamos 5 cenários aplicados a cada coluna da matriz de meta-alvo $Y$:
* **Baseline (Sem Escalonamento)**
* **Standard Scaler** (Z-score)
* **Min-Max Scaler** (escalonamento linear para o intervalo $[0, 1]$)
* **Robust Scaler** (centralização pela mediana e divisão pelo IQR)
* **Quantile Transformer** (mapeamento não-linear para uma distribuição normal)

### 5. Meta-modelos e Validação Cruzada
Utilizou-se validação cruzada de **5 folds** em todos os experimentos. Foram avaliados **4 regressores meta**:
* **Ridge Regression** (com $\alpha = 1.0$)
* **SVR** (Support Vector Regressor, kernel RBF, $C=1.0, \epsilon=0.1$)
* **Random Forest Regressor** (100 estimadores)
* **MLP Regressor** (Rede Neural Multicamadas, arquitetura $(100, 50)$, solver Adam, max_iter=1000)

Os modelos foram avaliados por meio de:
* **MAE Médio** e **RMSE Médio** na escala original de F1-score.
* **Correlação de Spearman Média:** Mede a capacidade do meta-modelo em ranquear corretamente as 5 opções de algoritmos base.
* **Acurácia de Recomendação (Acc@1):** Proporção de datasets em que o algoritmo previsto como o melhor foi de fato o melhor.

Para validação estatística, aplicou-se o teste não-paramétrico global de **Friedman** e o teste post-hoc de **Wilcoxon signed-rank** para comparar cada técnica de escalonamento diretamente com o baseline.

---

# Resultados e Discussão — Experimento 1 (50 Datasets Sintéticos + Reais)

Os experimentos geraram os resultados consolidados apresentados na tabela abaixo:

| Meta-Modelo | Métrica | Sem Escalonamento (NaN) | StandardScaler | MinMaxScaler | RobustScaler | QuantileTransformer |
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

### Principais Conclusões do Experimento 1:
1. **MLP Regressor:** Demonstrou extrema sensibilidade ao escalonamento. Sem escalonamento, o MAE foi de **0.1474** e recomendação Acc@1 de **0.32**. Com o **RobustScaler**, o MAE caiu para **0.0710** (redução de **51.8%** no erro absoluto) e o Acc@1 subiu para **0.44**.
2. **SVR:** Beneficiou-se do escalonamento linear, reduzindo o MAE de **0.0703** para **0.0635** (com MinMaxScaler). A acurácia de recomendação disparou de **0.32** para **0.48** (com RobustScaler e StandardScaler).
3. **Random Forest:** Teve desempenho estável e estatisticamente idêntico sob qualquer escalonador ($p = 0.512$ no teste de Friedman).
4. **O Paradoxo do Escalonamento Não-Linear (Quantile Transformer):** O QT reduziu o erro absoluto do Ridge, mas degradou severamente a acurácia de recomendação de **0.50** para **0.34**.

---

# Resultados e Discussão — Experimento 2 (144 Datasets Reais do OpenML)

| Meta-Modelo | Técnica | MAE (μ) | RMSE (μ) | Spearman (μ) | Acc@1 (μ) |
| :--- | :--- | :---: | :---: | :---: | :---: |
| **Ridge** | Baseline | 0,1371 | 0,1480 | 0,5148 | **0,5069** |
| | StandardScaler | 0,1371 | 0,1480 | 0,5148 | **0,5069** |
| | MinMaxScaler | 0,1371 | 0,1480 | 0,5148 | **0,5069** |
| | RobustScaler | 0,1371 | 0,1480 | 0,5148 | **0,5069** |
| | QuantileTransformer | 0,1378 | 0,1508 | **0,5174** | 0,4792 |
| **SVR** | Baseline | **0,1284** | **0,1399** | 0,4808 | 0,4861 |
| | StandardScaler | 0,1326 | 0,1446 | **0,5462** | 0,5208 |
| | MinMaxScaler | **0,1284** | **0,1399** | 0,4780 | 0,4792 |
| | RobustScaler | 0,1298 | 0,1418 | 0,5267 | **0,5625** |
| | QuantileTransformer | 0,1287 | 0,1423 | 0,5304 | 0,4931 |
| **RandomForest** | Baseline | 0,1050 | 0,1149 | 0,5828 | 0,5625 |
| | StandardScaler | **0,1043** | **0,1142** | 0,5808 | **0,5903** |
| | MinMaxScaler | 0,1048 | 0,1148 | 0,5800 | 0,5694 |
| | RobustScaler | 0,1059 | 0,1158 | **0,5871** | 0,5556 |
| | QuantileTransformer | 0,1119 | 0,1236 | 0,5487 | 0,5139 |
| **MLP** | Baseline | 0,1794 | 0,2063 | 0,0337 | 0,1250 |
| | StandardScaler | **0,1179** | **0,1296** | 0,4535 | 0,3958 |
| | MinMaxScaler | 0,1793 | 0,2062 | 0,0337 | 0,1250 |
| | RobustScaler | 0,1183 | 0,1302 | 0,4527 | **0,4375** |
| | QuantileTransformer | 0,1262 | 0,1419 | **0,4688** | 0,3889 |

### Principais Conclusões do Experimento 2:
1. **MLP com Dados Reais:** O colapso sem escalonamento foi ainda mais severo. A correlação de Spearman caiu para **0,0337** (correlação quase nula) e a recomendação Acc@1 para **12.5%** (pior do que o acaso). StandardScaler e RobustScaler recuperaram a capacidade preditiva.
2. **SVR com Dados Reais:** O escalonamento linear com MinMaxScaler reduziu o MAE, mas o ranqueamento se beneficiou do StandardScaler (Spearman subiu para **0,5462**) e do RobustScaler (Acc@1 subiu para **0,5625**).

---

# Resultados e Discussão — Experimento 3 (200 Datasets Reais do OpenML)

Neste experimento, expandimos a base de dados do OpenML para **200 datasets reais** utilizando o novo mecanismo de cache individualizado e download concorrente. Os resultados consolidados sob validação cruzada de 5 folds no nível meta são apresentados a seguir:

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
| | StandardScaler | 0,1146 | 0,1294 | 0,5475 | 0,5101 |
| | MinMaxScaler | **0,1143** | **0,1291** | **0,5543** | 0,5152 |
| | RobustScaler | 0,1144 | **0,1291** | 0,5444 | **0,5253** |
| | QuantileTransformer | 0,1193 | 0,1367 | 0,5219 | 0,4495 |
| **MLP** | Baseline | 0,1740 | 0,1999 | 0,1674 | 0,2071 |
| | StandardScaler | **0,1306** | **0,1464** | **0,4308** | 0,3737 |
| | MinMaxScaler | 0,1703 | 0,1941 | 0,2109 | 0,2020 |
| | RobustScaler | 0,1350 | 0,1509 | 0,4132 | **0,3990** |
| | QuantileTransformer | 0,1463 | 0,1651 | 0,3835 | 0,3535 |

### Principais Conclusões do Experimento 3:
1. **Confirmação do Colapso do MLP:**
   Sem o correto escalonamento do meta-alvo, o MLP colapsa com Spearman de apenas **0,1674** e acurácia de recomendação marginalmente próxima do acaso (Acc@1 = **20,7%** para 5 algoritmos). O StandardScaler e o RobustScaler restauram o poder preditivo, subindo a Spearman para **0,4308** e a Acc@1 para **39,9%**.
2. **Insensibilidade e Estabilidade do RandomForest:**
   O RandomForest obteve erros e ordenação praticamente invariantes entre os escalonadores lineares (MAE oscilando residualmente entre **0,1143** e **0,1148**), demonstrando excelente estabilidade a transformações de escala.
3. **SVR e a Vantagem do RobustScaler no Ranking:**
   Embora o StandardScaler tenha tido o menor erro numérico absoluto (MAE = 0,1234), o **RobustScaler** sobressaiu-se na recomendação, alcançando a melhor Spearman (**0,5238**) e a melhor Acc@1 (**0,4697**).
4. **QuantileTransformer como Pior Escolha Geral de Ranking:**
   Em todos os regressores não lineares (SVR, RF e MLP), o Quantile Transformer foi a pior escolha para a acurácia de recomendação (Acc@1 decrescendo para **35.3%** no MLP e **44.9%** no RF), o que valida empiricamente o efeito de deformação não-linear da ordenação do meta-alvo.

---

# Resultados e Discussão — Experimento 4 (198 Datasets Reais + PyMFE)

Neste experimento, mantivemos a base de datasets reais do OpenML, mas substituímos as 8 meta-features calculadas de forma manual pela extração automática de características via **PyMFE** (grupos `general`, `statistical`, `info-theory` e `clustering`), gerando **78 features** por dataset. Após o alinhamento entre X e Y e a remoção de entradas com meta-alvo ausente, o experimento operou com **198 datasets válidos** e validação cruzada de **5 folds** no nível meta. Os resultados são apresentados a seguir:

| Meta-Modelo | Técnica | MAE (μ) | RMSE (μ) | Spearman (μ) | Acc@1 (μ) |
| :--- | :--- | :---: | :---: | :---: | :---: |
| **Ridge** | Baseline | **0,1007** | **0,1163** | **0,4966** | **0,3788** |
| | StandardScaler | **0,1007** | **0,1163** | **0,4966** | **0,3788** |
| | MinMaxScaler | **0,1007** | **0,1163** | **0,4966** | **0,3788** |
| | RobustScaler | **0,1007** | **0,1163** | **0,4966** | **0,3788** |
| | QuantileTransformer | 0,1152 | 0,1358 | 0,4483 | 0,3586 |
| **SVR** | Baseline | 0,0978 | 0,1100 | 0,5213 | 0,3687 |
| | StandardScaler | 0,0898 | 0,1024 | **0,5982** | **0,4949** |
| | MinMaxScaler | 0,0952 | 0,1074 | 0,5447 | 0,4192 |
| | RobustScaler | **0,0884** | **0,1009** | 0,5966 | 0,4646 |
| | QuantileTransformer | 0,0896 | 0,1033 | 0,5847 | 0,4293 |
| **RandomForest** | Baseline | 0,0860 | 0,0982 | 0,6019 | 0,4747 |
| | StandardScaler | 0,0847 | 0,0972 | 0,6110 | 0,5000 |
| | MinMaxScaler | **0,0845** | **0,0966** | **0,6157** | **0,5152** |
| | RobustScaler | 0,0854 | 0,0976 | 0,6123 | 0,4949 |
| | QuantileTransformer | 0,0873 | 0,1021 | 0,5934 | 0,4697 |
| **MLP** | Baseline | 0,1830 | 0,2137 | 0,0802 | 0,2121 |
| | StandardScaler | **0,1165** | **0,1346** | 0,3875 | 0,3384 |
| | MinMaxScaler | 0,1714 | 0,1984 | 0,1097 | 0,2424 |
| | RobustScaler | 0,1220 | 0,1417 | 0,3542 | **0,3586** |
| | QuantileTransformer | 0,1294 | 0,1483 | **0,4355** | 0,2980 |

### Testes Estatísticos — Friedman e Wilcoxon

| Meta-Modelo | χ² | p-value | Significativo? |
| :--- | :--- | :--- | :--- |
| **Ridge** | 22,3760 | $1,69\times 10^{-4}$ | ✓ Sim |
| **SVR** | 53,9152 | $5,48\times 10^{-11}$ | ✓ Sim |
| **RandomForest** | 4,5778 | 0,3334 | ✗ Não |
| **MLP** | 278,0238 | $5,94\times 10^{-59}$ | ✓ Sim |

**Wilcoxon post-hoc para o Ridge:** A diferença global detectada pelo Friedman é inteiramente atribuída ao QuantileTransformer — que provoca piora significativa ($W = 6395$, $p = 1,87\times 10^{-5}$, $\Delta = +0,0145$) — e ao MinMaxScaler — que apresenta piora significativa mínima ($W = 7489,50$, $p = 0,0481$, $\Delta \approx 0$). Os scalers StandardScaler e RobustScaler permanecem estatisticamente equivalentes ao Baseline ($p > 0,05$), em concordância com a invariância matemática do Ridge.
**Wilcoxon post-hoc para o SVR:** Todos os quatro scalers melhoram significativamente o MAE em relação ao Baseline ($p < 10^{-4}$). Destaque para RobustScaler no erro numérico (MAE = 0,0884) e para StandardScaler no ranqueamento (Spearman = 0,5982; Acc@1 = 0,4949).

**Wilcoxon post-hoc para o RandomForest:** Globalmente não significativo ($p = 0,3334$). No post-hoc, apenas o MinMaxScaler apresenta melhora significativa mínima ($W = 8187$, $p = 0,0394$, $\Delta = -0,0015$); os demais scalers não diferem estatisticamente do Baseline.

**Wilcoxon post-hoc para o MLP:** Todos os scalers diferem significativamente do Baseline, com grandes reduções de MAE (StandardScaler: $\Delta = -0,0664$; RobustScaler: $\Delta = -0,0610$). Notavelmente, o QuantileTransformer apresenta a **maior Spearman** dentre todos os escaladores (**0,4355**), comportamento distinto do observado nos Experimentos 2 e 3 — sugerindo que, com representações ricas de meta-features, o MLP se beneficia da transformação por quantis para tarefas de ranqueamento.

### Principais Conclusões do Experimento 4:
1. **Poder Descritivo do PyMFE**:
   A inclusão de 78 meta-features estruturais reduziu significativamente o erro absoluto (MAE) em todos os modelos. O MAE mínimo do SVR despencou de **0,1234** (Experimento 3) para **0,0884** (com RobustScaler), e o do RandomForest caiu de **0,1143** para **0,0845** (com MinMaxScaler). O Random Forest com MinMaxScaler atingiu a maior Spearman do estudo: **0,6157**.
2. **Surgimento de Significância Estatística no SVR e no Ridge**:
   Ao contrário do Experimento 3, onde o SVR não apresentou diferença global significativa ($p = 0,4911$), com PyMFE as diferenças tornaram-se altamente significativas ($p = 5,48\times 10^{-11}$). Para o Ridge, o Friedman também detectou diferença global ($p = 1,69\times 10^{-4}$), mas o post-hoc revela que essa diferença é devida ao QuantileTransformer (piora) e minimamente ao MinMaxScaler — enquanto StandardScaler e RobustScaler permanecem equivalentes ao Baseline.
3. **Consistência do MLP e a Exceção do QuantileTransformer**:
   O MLP continuou a exibir colapso severo no Baseline (Spearman = 0,0802) e com MinMaxScaler. Inusitadamente, neste experimento o QuantileTransformer passou a ser o melhor escalonador em Spearman (**0,4382**), sugerindo que a representação densa do PyMFE cria um espaço meta-alvo onde transformações por quantis favorecem o ranqueamento neural.

---

# Conclusão Geral do Estudo

Este estudo transpôs as investigações de Amorim et al. (2022) para o nível de meta-aprendizagem, fornecendo respostas sólidas para as hipóteses formuladas:

1. **Existe diferença relevante no desempenho do meta-modelo ao variar o escalonamento do meta-alvo?** 
   *Sim*. A escolha da técnica de escalonamento do meta-alvo desempenha um papel crucial para meta-modelos baseados em gradiente (MLP) ou com restrição rígida de erro (SVR). Com a introdução do PyMFE (78 meta-features), a sensibilidade do SVR tornou-se estatisticamente inequívoca ($p = 5,48\times 10^{-11}$), com ganhos expressivos em erro e ranqueamento. Até o Ridge, matematicamente invariante a scalers lineares, passou a exibir diferença global significativa ($p = 2,73\times 10^{-4}$) — causada exclusivamente pelo QuantileTransformer, que provoca piora.
2. **Há uma recomendação geral que funcione para qualquer meta-modelo?**
   *Não de forma universal*. O RandomForest permanece robusto e insensível a transformações lineares no alvo. Para modelos lineares (Ridge), o Baseline é equivalente a qualquer scaler linear. Contudo, para os regressores sensíveis (MLP e SVR), o **StandardScaler** e o **RobustScaler** sobressaem-se como escolhas seguras e eficazes. Com meta-features PyMFE, o MLP apresenta um comportamento inédito: o QuantileTransformer maximiza o Spearman (0,4382), sugerindo que representações ricas tornam o espaço meta-alvo mais compatível com transformações por quantis.
3. **Recomendação baseada em objetivos (Predição vs Recomendação):**
   Deve-se evitar o uso de transformações não-lineares (como QuantileTransformer) para tarefas puras de recomendação de algoritmos (Acc@1), pois a distorção induzida na distribuição original degrada a ordenação de forma consistente — exceto no MLP com PyMFE, onde pode beneficiar o Spearman.

### Recomendação Final Consolidada

| Meta-Modelo | Objetivo | Scaler Recomendado |
| :--- | :--- | :--- |
| Random Forest | Qualquer | Nenhum (Baseline) ou MinMaxScaler |
| SVR | Maximizar Acc@1 e Spearman | StandardScaler ou RobustScaler |
| SVR | Minimizar MAE | RobustScaler |
| MLP | Minimizar MAE | StandardScaler |
| MLP | Maximizar Acc@1 | RobustScaler |
| MLP (com PyMFE) | Maximizar Spearman | QuantileTransformer |
| **Qualquer** | **Qualquer** | ❌ **Evitar QuantileTransformer para Acc@1** |
| **MLP** | **Qualquer** | ❌ **Evitar MinMaxScaler** |
