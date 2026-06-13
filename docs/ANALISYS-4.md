# Análise do Experimento com PyMFE (FINAL.ipynb) — Experimento 4

Esta análise detalha os resultados do **Experimento 4**, conduzido no notebook `FINAL.ipynb` após a substituição das 8 meta-features manuais pela extração automática de meta-features estruturadas com a biblioteca **PyMFE** (grupos: `general`, `statistical`, `info-theory`, e `clustering`). O experimento operou com **200 datasets válidos** do OpenML (após alinhamento e remoção de entradas com meta-alvo ausente), utilizando validação cruzada de **5 folds** no nível meta.

---

## 1. Caracterização do Experimento

### 1.1 Diferenças Metodológicas entre os Experimentos

| Dimensão                             | Experimento 1 (REPORT.md)     | Experimento 2 (ANALISYS-2.md) | Experimento 3 (ANALISYS-3.md) | Experimento 4 (Atual)                                          |
| :------------------------------------ | :---------------------------- | :---------------------------- | :---------------------------- | :------------------------------------------------------------- |
| **Número de Datasets**         | 50 (4 reais + 46 sintéticos) | 144 datasets reais            | 200 datasets reais            | **200 datasets válidos**                               |
| **Extração de Meta-features** | Manual (8 features)           | Manual (8 features)           | Manual (8 features)           | **PyMFE (78 features)**                                  |
| **Grupos PyMFE**                | N/A                           | N/A                           | N/A                           | `general`, `statistical`, `info-theory`, `clustering`  |
| **Estrutura de Cache**          | Sem cache                     | CSV único (420MB)            | CSVs individuais              | CSVs individuais                                               |
| **Concorrência**               | Monothread                    | Monothread                    | Multithread                   | Multithread                                                    |
| **Validação Cruzada**         | 5-Fold                        | 5-Fold                        | 5-Fold                        | **5-Fold**                                               |
| **Tratamento de Outliers (X)**  | N/A                           | N/A                           | N/A                           | **Clip local ([-1e10, 1e10]) e imputação por mediana** |

---

## 2. Resultados Consolidados do Pipeline de Meta-Aprendizagem (PyMFE)

Os resultados consolidados obtidos via validação cruzada de 5 folds no nível meta estão tabulados abaixo.

### Tabela de Resultados por Meta-Modelo e Técnica de Escalonamento (Experimento 4)

| Meta-Modelo            | Técnica            |     MAE (μ)     |    RMSE (μ)    |  Spearman (μ)  |    Acc@1 (μ)    |
| :--------------------- | :------------------ | :--------------: | :--------------: | :--------------: | :--------------: |
| **Ridge**        | Baseline            | **0,1007** | **0,1163** | **0,4966** | **0,3788** |
|                        | StandardScaler      | **0,1007** | **0,1163** | **0,4966** | **0,3788** |
|                        | MinMaxScaler        | **0,1007** | **0,1163** | **0,4966** | **0,3788** |
|                        | RobustScaler        | **0,1007** | **0,1163** | **0,4966** | **0,3788** |
|                        | QuantileTransformer |      0,1152      |      0,1358      |      0,4483      |      0,3586      |
| **SVR**          | Baseline            |      0,0978      |      0,1100      |      0,5213      |      0,3687      |
|                        | StandardScaler      |      0,0898      |      0,1024      | **0,5982** | **0,4949** |
|                        | MinMaxScaler        |      0,0952      |      0,1074      |      0,5447      |      0,4192      |
|                        | RobustScaler        | **0,0884** | **0,1009** |      0,5966      |      0,4646      |
|                        | QuantileTransformer |      0,0896      |      0,1033      |      0,5847      |      0,4293      |
| **RandomForest** | Baseline            |      0,0860      |      0,0982      |      0,6019      |      0,4747      |
|                        | StandardScaler      |      0,0847      |      0,0972      |      0,6110      |      0,5000      |
|                        | MinMaxScaler        | **0,0845** | **0,0966** | **0,6157** | **0,5152** |
|                        | RobustScaler        |      0,0854      |      0,0976      |      0,6123      |      0,4949      |
|                        | QuantileTransformer |      0,0873      |      0,1021      |      0,5934      |      0,4697      |
| **MLP**          | Baseline            |      0,1830      |      0,2137      |      0,0802      |      0,2121      |
|                        | StandardScaler      | **0,1165** | **0,1346** |      0,3875      |      0,3384      |
|                        | MinMaxScaler        |      0,1714      |      0,1984      |      0,1097      |      0,2424      |
|                        | RobustScaler        |      0,1220      |      0,1417      |      0,3542      | **0,3586** |
|                        | QuantileTransformer |      0,1294      |      0,1483      | **0,4355** |      0,2980      |

---

## 3. Análise Estatística (Experimento 4)

### 3.1 Teste de Friedman (Significância Global)

O teste de Friedman avalia se há diferença estatisticamente significativa no MAE de validação entre as 5 técnicas de escalonamento testadas para cada meta-modelo:

| Meta-Modelo            | χ²               | p-value                           | Significativo?   |
| :--------------------- | :----------------- | :-------------------------------- | :--------------- |
| **Ridge**        | 22,3760            | $1,69\times 10^{-4}$            | **✓ Sim** |
| **SVR**          | 53,9152            | $5,48\times 10^{-11}$           | **✓ Sim** |
| **RandomForest** | 4,5778             | 0,3334                            | ✗ Não          |
| **MLP**          | **278,0238** | **$5,94\times 10^{-59}$** | **✓ Sim** |

### 3.2 Testes de Wilcoxon Post-Hoc (Cada Scaler vs. Baseline)

Comparações pareadas de cada técnica de escalonamento contra a performance bruta (Baseline):

* **Ridge** ($p = 1,69\times 10^{-4}$):

  - **StandardScaler**: $W = 7501,00$ ($p = 0,2461$, $\Delta = +0,0000$) — **✗ Não significativo**
  - **MinMaxScaler**: $W = 7489,50$ ($p = 0,0481$, $\Delta = +0,0000$) — **✓ Piora Significativa (mínima)**
  - **RobustScaler**: $W = 7630,50$ ($p = 0,1180$, $\Delta = +0,0000$) — **✗ Não significativo**
  - **QuantileTransformer**: $W = 6395,00$ ($p = 1,87\times 10^{-5}$, $\Delta = +0,0145$) — **✓ Piora Significativa**
* **SVR** ($p = 5,48\times 10^{-11}$):

  - **StandardScaler**: $W = 6452,00$ ($p = 2,56\times 10^{-5}$, $\Delta = -0,0080$) — **✓ Melhora Significativa**
  - **MinMaxScaler**: $W = 5041,00$ ($p = 2,56\times 10^{-9}$, $\Delta = -0,0026$) — **✓ Melhora Significativa**
  - **RobustScaler**: $W = 5571,00$ ($p = 1,15\times 10^{-7}$, $\Delta = -0,0093$) — **✓ Melhora Significativa**
  - **QuantileTransformer**: $W = 6667,00$ ($p = 8,04\times 10^{-5}$, $\Delta = -0,0082$) — **✓ Melhora Significativa**
* **RandomForest** ($p = 0,3334$, global não significativo):

  - **StandardScaler**: $W = 8358,00$ ($p = 0,0645$, $\Delta = -0,0013$) — **✗ Não significativo**
  - **MinMaxScaler**: $W = 8187,00$ ($p = 0,0394$, $\Delta = -0,0015$) — **✓ Melhora Significativa (mínima)**
  - **RobustScaler**: $W = 9235,00$ ($p = 0,4458$, $\Delta = -0,0005$) — **✗ Não significativo**
  - **QuantileTransformer**: $W = 9706,00$ ($p = 0,8580$, $\Delta = +0,0013$) — **✗ Não significativo**
* **MLP** ($p = 5,94\times 10^{-59}$):

  - **StandardScaler**: $W = 2079,00$ ($p = 6,20\times 10^{-22}$, $\Delta = -0,0664$) — **✓ Melhora Significativa**
  - **MinMaxScaler**: $W = 2001,00$ ($p = 1,20\times 10^{-20}$, $\Delta = -0,0116$) — **✓ Melhora Significativa Mínima**
  - **RobustScaler**: $W = 1823,00$ ($p = 7,05\times 10^{-23}$, $\Delta = -0,0610$) — **✓ Melhora Significativa**
  - **QuantileTransformer**: $W = 3976,00$ ($p = 3,43\times 10^{-13}$, $\Delta = -0,0536$) — **✓ Melhora Significativa**

---

## 4. Discussão do Impacto do PyMFE

1. **Redução Geral do Erro Absoluto (MAE)**:
   A introdução das meta-features extraídas via `PyMFE` (78 features dos grupos `general`, `statistical`, `info-theory` e `clustering`) reduziu drasticamente o erro numérico absoluto em todos os meta-modelos. No SVR, o menor MAE passou de **0,1234** (Experimento 3) para **0,0884** (Experimento 4) com o RobustScaler. No RandomForest, reduziu de **0,1143** para **0,0845**. Isso comprova que a descrição multidimensional do PyMFE é muito superior às 8 meta-features manuais.
2. **Melhoria da Capacidade de Ranqueamento (Spearman)**:
   A ordenação relativa predita obteve ganhos expressivos. A correlação de Spearman do RandomForest alcançou **0,6157** (com MinMaxScaler), comparada ao máximo de **0,5543** do Experimento 3. O SVR também subiu para **0,5982** (StandardScaler) contra **0,5238** anteriormente.
3. **Mudança de Comportamento no SVR e no Ridge**:
   No Experimento 3, a variação de escaladores no SVR não foi estatisticamente significativa globalmente ($p = 0,4911$). Com o PyMFE, a diferença tornou-se **altamente significativa** ($p = 5,48\times 10^{-11}$), com todos os scalers superando o baseline. O Ridge, por sua invariância matemática a transformações lineares, manteve resultados idênticos entre os quatro scalers lineares — porém o Friedman agora detecta diferença global significativa ($p = 1,69\times 10^{-4}$) exclusivamente em razão do QuantileTransformer, que provocou **piora significativa** (Wilcoxon $p = 1,87\times 10^{-5}$, $\Delta = +0,0145$). O MinMaxScaler também apresentou piora significativa mínima ($p = 0,0481$, $\Delta \approx 0$).
4. **Comportamento Particular do MLP com PyMFE**:
   O MLP permanece como o modelo mais sensível ao escalonamento. Com as meta-features PyMFE, o baseline colapsa a Spearman para **0,0802**. Notavelmente, o **QuantileTransformer** passa a ter a maior Spearman entre todos os escaladores (**0,4355**), embora o StandardScaler minimize o MAE (**0,1165**) e o RobustScaler maximize o Acc@1 (**0,3586**). Isso indica que, com representações ricas de meta-features, o MLP se beneficia de diferentes transformações dependendo do objetivo.
5. **RandomForest: Robustez Confirmada com Nuance**:
   O Friedman não detectou diferença global significativa no RandomForest ($p = 0,3334$). No post-hoc, apenas o MinMaxScaler apresentou melhora estatisticamente significativa ($p = 0,0394$, $\Delta = -0,0015$), porém de magnitude mínima. Os demais scalers permanecem estatisticamente equivalentes ao Baseline, confirmando a robustez estrutural do Random Forest a transformações de escala no meta-alvo.
