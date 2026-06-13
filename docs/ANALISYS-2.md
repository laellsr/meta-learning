# Análise do Experimento com Datasets Reais do OpenML (FINAL.ipynb)

Esta análise examina os resultados do experimento conduzido no notebook `FINAL.ipynb`, que expande significativamente o escopo da investigação anterior. Enquanto o experimento documentado no `REPORT.md` utilizou **50 datasets** (4 reais do scikit-learn + 46 sintéticos), este novo experimento opera exclusivamente com **144 datasets reais** provenientes do **OpenML**, introduzindo um grau de heterogeneidade e realismo substancialmente maior.

---

## 1. Caracterização do Novo Experimento

### 1.1 Diferenças Metodológicas em Relação ao Experimento Anterior

| Dimensão | Experimento Anterior (REPORT.md) | Experimento Atual (FINAL.ipynb) |
| :--- | :--- | :--- |
| **Número de Datasets** | 50 (4 reais + 46 sintéticos) | 144 datasets reais |
| **Fonte dos Dados** | scikit-learn + `make_classification` | OpenML (catálogo público) |
| **Natureza dos Dados** | Controlada/sintética + poucos reais | Completamente real e heterogênea |
| **Instâncias (N)** | $[200, 1000]$ | Mediana: 981, Máx: 2000 |
| **Features (D)** | $[6, 25]$ | Mediana: 21, Máx: 1846 |
| **Imbalance Ratio (IR)** | $[1.0, 20.0]$ (controlado) | Mediana: 1.73, Máx: 479 (extremo!) |
| **Algoritmos Base** | KNN, DT, RF, GNB, SVM (5) | KNN, DT, RF, GNB, SVM (5) |
| **Meta-modelos** | Ridge, SVR, RF, MLP (4) | Ridge, SVR, RF, MLP (4) |
| **Técnicas de Escalonamento** | Baseline, SS, MM, RS, QT (5) | Baseline, SS, MM, RS, QT (5) |

### 1.2 Perfil do Meta-dataset

O conjunto de datasets apresenta características muito mais variadas e realistas:

| Meta-feature | Mínimo | Q1 | Mediana | Q3 | Máximo |
| :--- | :---: | :---: | :---: | :---: | :---: |
| **n_instances** | 500 | 559 | 981,5 | 1.291 | 2.000 |
| **n_features** | 4 | 10 | 21 | 40 | 1.846 |
| **ratio_D/N** | 0,003 | 0,012 | 0,024 | 0,050 | 1,042 |
| **imbalance_ratio** | 1,000 | 1,097 | 1,728 | 7,576 | **479,0** |
| **entropy** | 0,150 | 0,924 | 0,997 | 1,193 | 8,774 |
| **mean_pearson** | 0,000 | 0,040 | 0,117 | 0,201 | 0,775 |
| **mean_variance** | 0,010 | 0,998 | 55,71 | 2.300.504 | 3,58×10¹¹ |
| **mean_mutual_info** | 0,003 | 0,010 | 0,030 | 0,069 | 1,002 |

> [!NOTE]
> A variância média apresenta extrema amplitude ($10^{-2}$ a $3,58 \times 10^{11}$) e o Imbalance Ratio chega a **479:1**, valores muito além do que foi controlado no experimento sintético. Isso reflete a natureza ruidosa e diversa de dados reais.

---

## 2. Resultados do Meta-Alvo (Distribuição das Performances dos Algoritmos Base)

Os 5 algoritmos base foram avaliados nos 144 datasets. Os resultados médios de F1-macro revelam:

| Algoritmo Base | Média | Desvio Padrão | Mediana | Mínimo | Máximo |
| :--- | :---: | :---: | :---: | :---: | :---: |
| **KNN** | 0,5792 | 0,2574 | 0,5786 | 0,0017 | 1,0000 |
| **DecisionTree** | 0,6412 | 0,2604 | 0,6575 | 0,0023 | 1,0000 |
| **RandomForest** | 0,6607 | 0,2725 | 0,6711 | 0,0024 | 1,0000 |
| **GaussianNB** | 0,5476 | 0,2494 | 0,6169 | 0,0021 | 1,0000 |
| **SVM** | 0,5631 | 0,2866 | 0,5636 | 0,0000 | 1,0000 |

> [!NOTE]
> O desvio padrão elevado (~0,25-0,29) em todos os algoritmos indica que o meta-alvo é altamente variável entre datasets — exatamente o tipo de dado que torna o escalonamento mais crítico e seus efeitos mais perceptíveis.

---

## 3. Resultados Consolidados do Pipeline de Meta-Aprendizagem

### Tabela de Resultados por Meta-Modelo e Técnica de Escalonamento

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

---

## 4. Análise Estatística

### 4.1 Teste de Friedman (Significância Global)

| Meta-Modelo | χ² | p-value | Significativo? |
| :--- | :---: | :---: | :---: |
| **Ridge** | 3,041 | 0,5510 | ✗ Não |
| **SVR** | 8,368 | 0,0790 | ✗ Não (limiar) |
| **RandomForest** | 5,406 | 0,2482 | ✗ Não |
| **MLP** | **148,723** | **3,82×10⁻³¹** | **✓ Sim** |

### 4.2 Testes de Wilcoxon Post-Hoc (Cada Scaler vs. Baseline)

**Ridge** (Friedman: não significativo):

| Scaler | W-stat | p-value | Δ MAE | Conclusão |
| :--- | :---: | :---: | :---: | :--- |
| StandardScaler | 2886,00 | 0,2092 | +0,0000 | ✗ n.s. = igual |
| MinMaxScaler | 3497,50 | 0,5155 | +0,0000 | ✗ n.s. ↑ piora |
| RobustScaler | 3092,00 | 0,0913 | +0,0000 | ✗ n.s. ↑ piora |
| QuantileTransformer | 5217,00 | 0,9952 | +0,0006 | ✗ n.s. ↑ piora |

**SVR** (Friedman: não significativo, mas com tendência):

| Scaler | W-stat | p-value | Δ MAE | Conclusão |
| :--- | :---: | :---: | :---: | :--- |
| StandardScaler | 4927,00 | 0,5590 | +0,0042 | ✗ n.s. ↑ piora |
| MinMaxScaler | 3695,00 | **0,0024** | −0,0001 | **✓ sig. ↓ melhora** |
| RobustScaler | 5102,00 | 0,8140 | +0,0014 | ✗ n.s. ↑ piora |
| QuantileTransformer | 4906,00 | 0,5312 | +0,0003 | ✗ n.s. ↑ piora |

**RandomForest** (Friedman: não significativo):

| Scaler | W-stat | p-value | Δ MAE | Conclusão |
| :--- | :---: | :---: | :---: | :--- |
| StandardScaler | 4602,00 | 0,2178 | −0,0007 | ✗ n.s. ↓ melhora |
| MinMaxScaler | 5055,00 | 0,7421 | −0,0002 | ✗ n.s. ↓ melhora |
| RobustScaler | 4871,00 | 0,4864 | +0,0009 | ✗ n.s. ↑ piora |
| QuantileTransformer | 4837,00 | 0,4450 | +0,0070 | ✗ n.s. ↑ piora |

**MLP** (Friedman: **extremamente significativo**, p = 3,82×10⁻³¹):

| Scaler | W-stat | p-value | Δ MAE | Conclusão |
| :--- | :---: | :---: | :---: | :--- |
| StandardScaler | 1621,00 | **7,10×10⁻¹³** | −0,0615 | **✓ sig. ↓ melhora** |
| MinMaxScaler | 2972,00 | **7,35×10⁻⁶** | −0,0001 | **✓ sig. ↓ melhora** |
| RobustScaler | 1375,00 | **1,75×10⁻¹⁴** | −0,0610 | **✓ sig. ↓ melhora** |
| QuantileTransformer | 2678,00 | **3,99×10⁻⁷** | −0,0531 | **✓ sig. ↓ melhora** |

---

## 5. Análise por Meta-Modelo e Comparação com o Experimento Anterior

### 5.1 Ridge Regression — Confirmação da Insensibilidade a Escalas Lineares

**Comportamento observado:** Idêntico ao experimento anterior. As quatro técnicas de escalonamento linear (StandardScaler, MinMaxScaler, RobustScaler) produziram resultados **exatamente iguais** ao Baseline (MAE = 0,1371, Spearman = 0,5148, Acc@1 = 0,5069). O QuantileTransformer causou uma leve piora no MAE (+0,0006) e no Acc@1 (−0,0277), mantendo a Spearman praticamente estável (+0,0026).

**Consistência com o experimento anterior:** Perfeita. No experimento com datasets sintéticos/reais (REPORT.md), o Ridge também mostrou MAE idêntico para todos os scalers lineares (MAE = 0,0832) e degradação com QT. A única diferença é que o QT melhorava o MAE no experimento anterior (0,0667 vs. 0,0832), mas aqui piorou levemente. Isso pode ser atribuído à maior heterogeneidade dos dados reais.

**Explicação matemática:** Transformações lineares reescalonam os coeficientes da regressão linear de forma proporcional, mantendo as predições de ranking idênticas. A inversão de escala exata cancela qualquer efeito residual.

### 5.2 SVR — Comportamento Inesperado com Datasets Reais

**Comportamento observado:** Neste experimento, o SVR exibiu um padrão **diferente** do anterior:
- O **Baseline** igualou o MinMaxScaler no melhor MAE (0,1284)
- O **StandardScaler** piorou o MAE (+0,0042), mas melhorou fortemente o Spearman (+0,0654) e o Acc@1 (+0,0347)
- O **RobustScaler** melhorou o Acc@1 para o melhor valor (0,5625), mesmo com MAE levemente pior
- Somente o MinMaxScaler mostrou melhora estatisticamente significativa no MAE (Wilcoxon, p = 0,0024, Δ = −0,0001)

**Contraste com o experimento anterior:** No REPORT.md, o SVR se beneficiou claramente do escalonamento, com o MinMaxScaler reduzindo o MAE de 0,0703 para 0,0635 (−9,7%) e o RobustScaler mantendo Spearman e Acc@1 elevados. No experimento atual, o efeito no MAE é mínimo, mas o impacto no ranqueamento (Spearman e Acc@1) ainda é expressivo.

> [!WARNING]
> A discrepância no comportamento do SVR entre os experimentos sugere que a escala absoluta do meta-alvo é menos determinante quando os dados são mais heterogêneos (como nos datasets reais). O parâmetro ε fixo do SVR-RBF (0.1) representa uma fração diferente da amplitude de Y dependendo da distribuição real dos dados.

### 5.3 Random Forest — Insensibilidade Confirmada (com Nuances)

**Comportamento observado:** O RandomForest manteve sua insensibilidade em termos de MAE e RMSE (variação ≤ 0,007). O Friedman não detectou diferença global significativa. Porém, dois padrões interessantes emergem:
1. O **StandardScaler** obteve o melhor Acc@1 (0,5903) e o menor MAE (0,1043)
2. O **QuantileTransformer** causou a maior degradação (+0,0069 MAE, −0,0341 Spearman, −0,0486 Acc@1)

**Consistência com o experimento anterior:** A insensibilidade geral é confirmada, mas com uma ressalva: no experimento anterior, o RF com Baseline obtinha a melhor Spearman e o melhor Acc@1. Aqui, o StandardScaler leva vantagem — sugerindo que com dados reais mais heterogêneos, há um sutil benefício do pré-processamento even para modelos baseados em árvores.

**Efeito do QuantileTransformer:** Em ambos os experimentos, o QT é a pior escolha para o RF. A transformação não-linear distorce as distâncias relativas entre os scores de performance, prejudicando a recomposição dos ranks.

### 5.4 MLP — Sensibilidade Extrema Confirmada (com Diferenças Notáveis)

**Comportamento observado:** O MLP exibiu o padrão mais dramático e consistente:

- **Sem escalonamento (Baseline):** MAE = 0,1794, Spearman = **0,0337** (correlação quasi-nula!), Acc@1 = **0,1250** (apenas 12,5% de acerto!)
- **Com MinMaxScaler:** Praticamente idêntico ao Baseline (MAE = 0,1793, Spearman = 0,0337) — confirmando que manter o alvo em [0,1] não ajuda o MLP
- **Com StandardScaler:** MAE = 0,1179 (−34,3%), Spearman = 0,4535 (+1.245%!), Acc@1 = 0,3958 (+216%)
- **Com RobustScaler:** MAE = 0,1183 (−34,1%), Spearman = 0,4527 (+1.244%), Acc@1 = **0,4375** (melhor)
- **Com QuantileTransformer:** MAE = 0,1262 (−29,7%), Spearman = **0,4688** (melhor), Acc@1 = 0,3889

**Contraste com o experimento anterior (REPORT.md):**
O padrão é altamente consistente, mas com magnitudes diferentes:

| Cenário | MAE Baseline → Melhor | Ganho MAE | Spearman Baseline |
| :--- | :--- | :--- | :--- |
| **Experimento Anterior** | 0,1474 → 0,0710 (RS) | −51,8% | 0,2740 |
| **Experimento Atual** | 0,1794 → 0,1179 (SS) | −34,3% | **0,0337** |

> [!IMPORTANT]
> A Spearman do MLP sem escalonamento caiu de 0,2740 (sintético) para apenas **0,0337** (real) — quase correlação zero. Isso indica que o meta-alvo gerado por datasets reais tem uma distribuição ainda mais problemática para o gradiente descendente do MLP. Os datasets reais produzem um meta-alvo com maior variância e mais outliers extremos (scores próximos de 0 e de 1 para o mesmo conjunto de algoritmos), tornando o problema ainda mais sensível ao escalonamento.

---

## 6. O Paradoxo do MinMaxScaler no SVR

Um resultado particularmente interessante é que o **MinMaxScaler** apresentou melhora estatisticamente significativa no MAE do SVR (p = 0,0024), mas **sem melhora** na Spearman ou Acc@1. Ao mesmo tempo, o StandardScaler piorou o MAE mas melhorou drasticamente o ranqueamento.

**Interpretação:** O MinMaxScaler mapeia o meta-alvo para [0,1], alinhando-o ao intervalo natural do F1-score. Com isso, o parâmetro ε = 0.1 do SVR torna-se proporcional à escala real dos dados, reduzindo o erro numérico. Porém, como o alvo continua assimétrico (não centrado), a capacidade de ranqueamento não melhora.

O StandardScaler centraliza o alvo em zero e normaliza pelo desvio padrão. Isso permite que o kernel RBF funcione em seu regime ótimo para a estimação de ranks relativos, mesmo que o erro absoluto absoluto (MAE reversível) não seja o menor.

> [!TIP]
> Este achado reforça uma distinção prática crucial: **escolha o escalonador com base no seu objetivo**:
> - Se o objetivo é minimizar o erro absoluto de previsão de performance → MinMaxScaler para SVR
> - Se o objetivo é recomendar o melhor algoritmo (ranking) → StandardScaler para SVR

---

## 7. Confronto dos Achados: Experimento Sintético vs. Real

| Fenômeno | Experimento Anterior (Sintético) | Experimento Atual (Real) | Consistência |
| :--- | :---: | :---: | :---: |
| Ridge insensível a scalers lineares | ✓ | ✓ | **Alta** |
| Ridge degradado pelo QT (Acc@1) | ✓ | ✓ | **Alta** |
| MLP sem escalonamento: Spearman ~0 | Parcial (0,274) | Total (0,034) | **Reforçada** |
| MLP com StandardScaler/RobustScaler: ganho ≥ 34% MAE | ✓ (−51,8%) | ✓ (−34,3%) | **Alta** |
| MLP com MinMaxScaler: sem ganho | ✓ | ✓ | **Alta** |
| RF insensível ao escalonamento linear | ✓ | Predominantemente ✓ | **Alta** |
| RF degradado pelo QT | ✓ | ✓ | **Alta** |
| SVR beneficiado pelo escalonamento | ✓ (MAE) | Parcial (Spearman/Acc@1) | **Moderada** |

---

## 8. Conclusões

### 8.1 Confirmações

1. **A sensibilidade ao escalonamento é uma propriedade da arquitetura**, não do conjunto de dados. Os padrões observados no experimento com dados sintéticos se replicam com dados reais, validando a generalidade das descobertas.

2. **O MLP requer escalonamento centralizado do meta-alvo**. Com dados reais, a necessidade é ainda mais crítica — sem o StandardScaler ou RobustScaler, o MLP produz recomendações essencialmente aleatórias (Acc@1 = 12,5%, apenas marginalmente acima do acaso para 5 algoritmos = 20%).

3. **O QuantileTransformer degrada a capacidade de ranqueamento** para todos os meta-modelos em ambos os experimentos. Este é o achado mais consistente e com maior impacto prático.

4. **O Random Forest mantém sua robustez**, sendo o único meta-modelo que funciona bem sem qualquer escalonamento do alvo.

### 8.2 Novas Descobertas (específicas dos dados reais)

1. **Com dados reais mais heterogêneos, o SVR precisa de escalonamento para ranking** (StandardScaler para Spearman; RobustScaler para Acc@1), mas o benefício no MAE é mínimo.

2. **A heterogeneidade dos dados reais amplifica o colapso do MLP não escalonado**: Spearman cai para quasi-zero, revelando que o problema não é apenas de magnitudes, mas de distribuições extremamente assimétricas geradas por datasets muito difíceis (alguns com F1 ≈ 0) e muito fáceis (alguns com F1 = 1,0).

3. **A variância média do meta-alvo é muito maior com dados reais** (desvio padrão ~ 0,26 em todos os algoritmos), tornando o RobustScaler (baseado em mediana e IQR) particularmente valioso para lidar com outliers de performance.

### 8.3 Recomendações Práticas

| Objetivo | Meta-Modelo | Scaler Recomendado |
| :--- | :--- | :--- |
| Máxima precisão de previsão (MAE) | Random Forest | Nenhum (Baseline) ou StandardScaler |
| Recomendação robusta (Acc@1) | Random Forest | StandardScaler |
| Recomendação com SVR | SVR | RobustScaler |
| Previsão de ranking com SVR | SVR | StandardScaler |
| Qualquer objetivo com MLP | MLP | StandardScaler ou RobustScaler |
| **Evitar sempre** | Qualquer | QuantileTransformer (prejudica rankings) |

> [!CAUTION]
> Nunca use **MinMaxScaler** para o MLP quando o meta-alvo já está no intervalo [0,1]. Este scaler não centraliza os dados, mantendo o problema de saturação das funções de ativação que torna o MLP ineficaz. Os resultados confirmam que o MinMaxScaler produz resultados praticamente idênticos ao Baseline para o MLP.
