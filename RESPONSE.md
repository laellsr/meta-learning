# Resposta aos Comentários do Professor Lucas Amorim

**Documento de Análise, Respostas e Plano de Ação das Revisões**  
- **Documento Anotado:** [`Relatorio_Lael_Lima_-_anotado_por_Lucas_18_06.pdf`](file:///c:/Users/lael_/OneDrive/Documentos/UFAL/Meta-aprendizagem/docs/teacher-report/Relatorio_Lael_Lima_-_anotado_por_Lucas_18_06.pdf)  
- **Aluno:** Lael de Lima Santa Rosa  
- **Avaliador / Orientador:** Prof. Lucas Amorim  
- **Data das Anotações:** 16/06/2026  
- **Status Geral:** Análise completa; plano de resposta estruturado; avaliação empírica em nível base executada com sucesso.

---

## 1. Visão Geral e Síntese do Parecer

O professor Lucas Amorim analisou o relatório e fez **6 anotações** no documento (sendo 4 comentários em balões com perguntas/críticas conceituais profundas e 2 destaques indicativos de escopo e terminologia).

Os apontamentos concentram-se em três eixos principais:

1. **Avaliação Prática em Nível Base (Ponto Crítico Principal — Página 3):**
   O trabalho avaliou amplamente o meta-modelo no nível meta (MAE, Spearman, Acc@1). O professor apontou a ausência crucial de uma **avaliação em nível base**: o usuário final de um sistema de meta-aprendizagem deseja saber qual a performance real média (ex: F1-score) ele obtém ao adotar o algoritmo recomendado no 1º lugar do ranking pelo meta-modelo, e como cada técnica de escalonamento do meta-alvo ($Y$) afeta essa performance prática final frente a baselines (Single Best, Escolha Aleatória) e ao Oráculo ideal.
2. **Precisão Conceitual e Terminológica: Padronização vs. Normalização do Meta-Alvo (Página 2):**
   Necessidade de explicitar com rigor que as técnicas sob investigação aplicam-se estritamente ao **meta-alvo ($Y$)** (mantendo as meta-features $X$ padronizadas), e substituir expressões informais ou impróprias como "escalonamento centralizado" pela terminologia matemática exata: **padronização do meta-alvo** (StandardScaler e RobustScaler, caracterizados por média $\mu=0$ ou mediana nula e dispersão unitária) vs. **normalização linear** (MinMaxScaler, restrito a $[0, 1]$ mantendo média positiva) vs. **transformações do meta-alvo** como termo geral.
3. **Classificação das Meta-Features (Página 4):**
   Correção conceitual: tanto as 8 meta-features iniciais quanto as 78 do PyMFE pertencem à mesma família *handcrafted* (analítica), não havendo dicotomia "manual vs automática" nem "básica vs handcrafted". A diferença entre os dois conjuntos é apenas de abrangência: as 8 métricas iniciais são um subconjunto restrito (dos grupos `general`/`statistical`) do espaço mais amplo já coberto pelo PyMFE.

---

## 2. Matriz Consolidada de Comentários

| ID | Página | Seção / Localização | Trecho Destacado | Comentário do Prof. Lucas Amorim | Tipo | Ação Requerida |
|---|:---:|---|---|---|---|---|
| **C1** | Pág. 1 | Resumo | *"quatro meta-modelos (Ridge, SVR, Random Forest, MLP)"* | *(Destaque indicativo sem texto)* | Escopo / Nomenclatura | Revisar consistência na distinção entre meta-modelos (regressores) e algoritmos base (classificadores). |
| **C2** | Pág. 2 | 1. Introdução / Contribuições | *"escalonamento"* | *"Do X, do Y ou de ambos?"* | Precisão Conceitual | Especificar explicitamente que o efeito estudado e o colapso do MLP decorrem da falta de escalonamento do **meta-alvo ($Y$)**. |
| **C3** | Pág. 2 | 1. Introdução / Contribuições | *"centralizado"* | *"Como assim?"* | Rigor Matemático | Corrigir a expressão imprecisa "escalonamento centralizado". Definir formalmente que a operação combinada de centralização em zero com escala por dispersão é denominada **padronização do meta-alvo** (StandardScaler e RobustScaler), diferenciando-a de normalização linear (MinMaxScaler) e ausência de pré-processamento (Baseline). |
| **C4** | Pág. 3 | 2.4 Métricas de Avaliação | *"Métricas de Avaliação"* | *"Todas essas métricas são para avaliar a saída do meta-modelo. O que mais senti falta no seu trabalho foi de uma avaliação em nível base. Ou seja, dado que o usuário do sistema segue a recomendação do meta-modelo, o quanto ele obtém de performance média em nível base ao escolher o primeiro do ranking recomendado, por exemplo? Seria necessário fazer isso para cada versão do meta-modelo (treinado com cada técnica de normalização aplicada ao y, incluindo sem normalizar) e comparar os resultados. Isso nos daria uma análise rica do ganho real obtido com o meta-modelo ajustado dessa forma."* | Metodológico / Experimental (**Crítico**) | Implementar métricas de nível base: F1 médio obtido pela recomendação Top-1, Perda em relação ao Oráculo (*Average Regret*) e Ganho sobre Baseline fixa; incluir nova tabela e análise comparativa. |
| **C5** | Pág. 4 | 3.1 Conjuntos de Dados / Exp. 4 | *"manuais"* | *"Tanto essas meta-features como as do PyMFE podem ser consideradas 'manuais'. No sentido de terem sido desenvolvidas manualmente ou analiticamente. Costuma-se chamá-las de handcrafted. Em outras palavras, essas meta-features que você está chamando de 'manuais' são na verdade um subconjunto das meta-features que já são calculadas pelo PyMFE."* | Terminologia / Literatura | Eliminar a dicotomia "manual vs PyMFE" e a variante posterior "básicas vs handcrafted"; adotar em todo o texto "subconjunto *handcrafted* de 8 meta-features gerais/estatísticas" vs "conjunto *handcrafted* completo de 78 descritores do PyMFE (que engloba as 8 anteriores como subconjunto)". |
| **C6** | Pág. 5 | 4.1 Distribuição do Meta-Alvo | *"base"* (em *algoritmos base*) | *(Destaque indicativo sem texto)* | Alinhamento de Conceito | Padronizar rigorosamente a distinção entre "nível base" e "nível meta", conectando à análise de nível base da Tabela 2. |

---

## 3. Detalhamento Item a Item: Diagnóstico, Respostas e Alterações no Texto

### Comentário C1 — Escopo e Nomenclatura dos Meta-Modelos
- **Localização:** Página 1, Resumo, linha 15 do relatório.
- **Texto Destacado:** `"quatro meta-modelos (Ridge, SVR, Random Forest, MLP)"`
- **Comentário do Professor:** *(Destaque indicativo).*
- **Diagnóstico:** O destaque pontua a definição dos 4 meta-modelos avaliados. É fundamental garantir que a taxonomia do trabalho esteja clara: no nível base temos 5 algoritmos classificadores (KNN, CART, RF, GNB e SVM), e no nível meta temos 4 regressores multi-saída com paradigmas indutivos complementares (linear, kernelizado, ensemble baseado em árvores e rede neural profunda).
- **Proposta de Resposta ao Professor:**
  > *"Agradecemos a observação. O destaque foi considerado para reforçar, já no resumo e na introdução, a separação categórica entre os 5 classificadores que operam no nível base e os 4 algoritmos de regressão empregados como meta-modelos, explicitando a diversidade de suas hipóteses indutivas."*
- **Ação no Relatório (`relatorio-sbc.tex`):**
  - Manter clara e inequívoca a distinção entre regressores (meta-nível) e classificadores (nível base).

---

### Comentário C2 — Ambiguidade: Escalonamento de X, de Y ou de ambos?
- **Localização:** Página 2, Seção 1 (Introdução), subseção de Contribuições Principais, 2º item.
- **Texto Destacado:** `"escalonamento"`
- **Comentário do Professor:** `"Do X, do Y ou de ambos?"`
- **Diagnóstico:** O texto original afirmava que *"o MLP, sem escalonamento centralizado, produz recomendações próximas ao acaso"*. Isso gerou ambiguidade: o leitor não sabe se a causa estava no pré-processamento das meta-features ($X$) ou no meta-alvo ($Y$). Como as meta-features $X$ sempre foram devidamente padronizadas com `StandardScaler` ajustado no fold de treino em todos os experimentos, o colapso decorre **exclusivamente da ausência de padronização do meta-alvo ($Y$)**.
- **Proposta de Resposta ao Professor:**
  > *"Excelente apontamento. De fato, a formulação anterior gerava ambiguidade. Em todos os experimentos conduzidos, as meta-features de entrada ($X$) foram invariavelmente padronizadas via StandardScaler no fold de treino. O colapso observado no MLP deve-se estritamente à ausência de escalonamento com centralização sobre a matriz de meta-alvo ($Y$). O texto foi reescrito para especificar expressamente 'do meta-alvo ($Y$)'."*
- **Ação no Relatório (`relatorio-sbc.tex`):**
  - **Antes:**
    ```latex
    \item Demonstração de que o MLP, sem escalonamento centralizado, produz
    recomendações próximas ao acaso com dados reais, resultado revertido pelo
    StandardScaler ou RobustScaler;
    ```
  - **Depois:**
    ```latex
    \item Demonstração de que o MLP colapsa na ausência de padronização do meta-alvo $Y$,
    gerando recomendações próximas ao acaso em dados reais mesmo quando as meta-features
    $X$ estão padronizadas --- falha superada ao remover o viés médio do alvo via
    StandardScaler ou RobustScaler;
    ```

---

### Comentário C3 — Rigor Conceitual: "Escalonamento Centralizado" vs. "Padronização do Meta-Alvo"
- **Localização:** Página 2, Seção 1 (Introdução), subseção de Contribuições Principais, 2º item.
- **Texto Destacado:** `"centralizado"`
- **Comentário do Professor:** `"Como assim?"`
- **Diagnóstico:** O uso da expressão "escalonamento centralizado" era conceitualmente impreciso e linguisticamente inadequado. Em estatística e aprendizado de máquina:
  1. **Escalonamento** (*Scaling*): refere-se puramente à alteração de escala ou amplitude através da multiplicação ou divisão por um fator de dispersão.
  2. **Centralização** (*Centering*): refere-se à translação dos dados pela subtração de uma medida de posição central (média $\mu$ ou mediana), transladando o centro da distribuição para a origem ($\mu = 0$ ou $\text{mediana} = 0$).
  3. **Padronização** (*Standardization* / *z-score*): é o termo técnico canônico para a composição simultânea de **centralização em zero** e **divisão por medida de dispersão unitária** (desvio padrão $\sigma$ ou IQR).
  Portanto, `StandardScaler` ($z = (y - \mu) / \sigma$) e `RobustScaler` ($z = (y - \text{mediana}) / \text{IQR}$) realizam formalmente **padronização**.
  Por outro lado, o `MinMaxScaler` realiza **normalização linear** no intervalo fechado $[0, 1]$, que não centraliza em zero (a média dos F1-scores permanece em torno de $0{,}65$).
  O colapso do MLP ocorre porque as redes neurais possuem pesos inicializados para prever saídas em torno de zero; sem a **padronização com média zero**, a função de perda MSE é dominada pelo aprendizado do offset constante positivo dos dados reais, anulando o gradiente necessário para aprender a ordenação relativa fina entre algoritmos base.
- **Proposta de Resposta ao Professor:**
  > *"O comentário do professor foi cirúrgico. A expressão 'escalonamento centralizado' era tecnicamente imprópria e foi completamente eliminada do manuscrito. A operação combinada de subtrair a média/mediana (centralização em zero) e dividir pela dispersão é formalmente denominada **Padronização** (Standardization). Adotamos a taxonomia precisa:
  > 1. **Padronização do meta-alvo** para o StandardScaler e RobustScaler (distribuições com centro em zero);
  > 2. **Normalização linear Min-Max** para o MinMaxScaler (compressão para $[0, 1]$ sem centralização em zero);
  > 3. **Transformação não-linear por quantis** para o QuantileTransformer;
  > 4. **Transformações do meta-alvo** como termo geral abrangente para as 5 condições avaliadas frente ao Baseline.
  > A Seção 2.3 e a discussão detalhada do MLP (Seção 4.4) foram reescritas para fundamentar matematicamente essa distinção e demonstrar que a eliminação do bias constante positivo via padronização é o fator que resgata a convergência do MLP."*
- **Ação no Relatório (`relatorio-sbc.tex`):**
  - **Antes:**
    ```latex
    o MLP, sem escalonamento centralizado, produz recomendações...
    ```
  - **Depois:**
    ```latex
    o MLP, na ausência de padronização do meta-alvo $Y$ (isto é, sem remoção de bias
    constante via StandardScaler ou RobustScaler, mesmo com meta-features $X$ padronizadas),
    produz recomendações com ganho prático em nível base inferior à escolha aleatória...
    ```
  - Reestruturação da Seção 2.3 explicitando formalmente as famílias matemáticas (sem transformação, padronização z-score, normalização linear min-max, padronização robusta e transformação quantílica).

---

### Comentário C4 — Avaliação em Nível Base (Comentário Crítico Principal)
- **Localização:** Página 3, Seção 2.4 (Métricas de Avaliação).
- **Texto Destacado:** `"Métricas de Avaliação"`
- **Comentário do Professor:**
  > *"Todas essas métricas são para avaliar a saída do meta-modelo. O que mais senti falta no seu trabalho foi de uma avaliação em nível base. Ou seja, dado que o usuário do sistema segue a recomendação do meta-modelo, o quanto ele obtém de performance média em nível base ao escolher o primeiro do ranking recomendado, por exemplo? Seria necessário fazer isso para cada versão do meta-modelo (treinado com cada técnica de normalização aplicada ao y, incluindo sem normalizar) e comparar os resultados. Isso nos daria uma análise rica do ganho real obtido com o meta-modelo ajustado dessa forma."*
- **Diagnóstico Profundo:**
  - O relatório limitava-se a avaliar o meta-nível (MAE, RMSE, Spearman e Acc@1).
  - Em um sistema de seleção de algoritmos, a métrica de valor real para o usuário é: **se o sistema me recomendou o algoritmo $a^*$, qual é o F1 real que eu vou atingir ao rodar esse algoritmo nos meus dados?**
  - Além disso, deve-se comparar essa performance contra:
    1. O **Oráculo Teórico** (desempenho máximo possível se acertássemos sempre o melhor classificador para cada dataset);
    2. A **Single Best Baseline** (usar sempre o melhor classificador padrão fixo em todos os datasets, sem meta-aprendizado — no caso, Random Forest com F1 médio de $0,7323$);
    3. A **Escolha Aleatória** (média de todos os classificadores em todos os datasets, $0,6668$).
- **Formalização Matemática:**
  Para cada dataset de teste $i \in \{1, \dots, N\}$:
  - $j^*(i) = \arg\max_{j} \hat{y}_{i, j}$ (algoritmo recomendado em 1º lugar pelo meta-modelo);
  - Performance em Nível Base obtida: $P_{\text{base}}(i) = y_{i, j^*(i)} \implies \overline{P}_{\text{base}} = \frac{1}{N}\sum_{i=1}^N y_{i, j^*(i)}$;
  - Oráculo: $P_{\text{oracle}}(i) = \max_j y_{i, j} \implies \overline{P}_{\text{oracle}} = \frac{1}{N}\sum_{i=1}^N \max_j y_{i, j}$;
  - Perda em Relação ao Oráculo (*Average Regret*): $\overline{\text{Regret}} = \overline{P}_{\text{oracle}} - \overline{P}_{\text{base}}$.
- **Resultados Empíricos Obtidos (Experimento 4 — 198 Datasets com PyMFE):**
  *(Executados via validação cruzada estratificada de 5 folds)*
  
  | Meta-Modelo | Scaler do Meta-Alvo ($Y$) | F1 Nível Base ($\mu$) | Desv. Padrão ($\sigma$) | Regret Médio ($\mu$) | Ganho vs. Single Best |
  |---|---|:---:|:---:|:---:|:---:|
  | *Referência* | *Oráculo Ideal* | *0,7579* | *0,1811* | *0,0000* | *+0,0256* |
  | *Referência* | *Single Best (Random Forest)* | *0,7323* | *0,1904* | *0,0256* | *0,0000* |
  | *Referência* | *Escolha Aleatória* | *0,6668* | *0,2118* | *0,0911* | *-0,0655* |
  | **SVR** | Baseline | 0,7296 | 0,1852 | 0,0283 | -0,0027 |
  | | StandardScaler | 0,7386 | 0,1848 | 0,0193 | +0,0063 |
  | | MinMaxScaler | 0,7332 | 0,1846 | 0,0247 | +0,0008 |
  | | **RobustScaler** | **0,7395** | 0,1841 | **0,0184** | **+0,0072** |
  | | QuantileTransformer | 0,7351 | 0,1865 | 0,0228 | +0,0028 |
  | **Random Forest** | Baseline | 0,7372 | 0,1869 | 0,0207 | +0,0049 |
  | | StandardScaler | 0,7362 | 0,1868 | 0,0217 | +0,0039 |
  | | **MinMaxScaler** | **0,7381** | 0,1854 | **0,0198** | **+0,0058** |
  | | RobustScaler | 0,7372 | 0,1870 | 0,0207 | +0,0049 |
  | | QuantileTransformer | 0,7364 | 0,1850 | 0,0215 | +0,0041 |
  | **Ridge** | Baseline / SS / MM / RS | 0,7214 | 0,1942 | 0,0365 | -0,0109 |
  | | QuantileTransformer | 0,7160 | 0,1914 | 0,0419 | -0,0163 |
  | **MLP** | Baseline | **0,6576** | 0,2190 | **0,1003** | **-0,0747** |
  | | MinMaxScaler | 0,6621 | 0,2131 | 0,0957 | -0,0702 |
  | | **StandardScaler** | **0,7062** | 0,2134 | **0,0517** | **-0,0261** |
  | | RobustScaler | 0,7028 | 0,2121 | 0,0551 | -0,0295 |
  | | QuantileTransformer | 0,7039 | 0,2044 | 0,0540 | -0,0285 |

- **Descobertas Críticas Reveladas por essa Avaliação:**
  1. **SVR sem escalonamento perde para a baseline fixa**, obtendo $0,7296$ (pior que sempre usar Random Forest, $0,7323$). Ao aplicar **RobustScaler**, o SVR sobe para **$0,7395$**, alcançando a **maior performance média de todo o experimento** e reduzindo o Regret para apenas **$0,0184$**!
  2. **O colapso do MLP no nível base é catastrófico**: no Baseline, sua performance média ($0,6576$) é **pior que a escolha aleatória ($0,6668$)**, com um Regret de mais de $10\%$. O uso de StandardScaler recupera quase $5$ pontos percentuais de F1 ($0,7062$), reduzindo o regret pela metade ($0,0517$).
  3. **O QuantileTransformer degrada o Ridge no nível base**, aumentando o Regret de $0,0365$ para $0,0419$.
- **Proposta de Resposta ao Professor:**
  > *"Concordamos integralmente com o professor. Essa era a lacuna metodológica mais importante do trabalho. Implementamos a avaliação completa em nível base para todos os meta-modelos e scalers, medindo o F1-macro médio do algoritmo recomendado no Top-1, o Regret Médio frente ao Oráculo e o Ganho em relação à Single Best (Random Forest) e à Escolha Aleatória. Os resultados foram incorporados na Seção 2.4 (formalização) e em uma nova subseção com tabela completa na Seção 4."*

---

### Comentário C5 — Nomenclatura: Meta-Features "Manuais" vs. PyMFE
- **Localização:** Página 4, Seção 3.1 (Conjuntos de Dados), parágrafo do Experimento 4.
- **Texto Destacado:** `"manuais"`
- **Comentário do Professor:**
  > *"Tanto essas meta-features como as do PyMFE podem ser consideradas 'manuais'. No sentido de terem sido desenvolvidas manualmente ou analiticamente. Costuma-se chamá-las de handcrafted. Em outras palavras, essas meta-features que você está chamando de 'manuais' são na verdade um subconjunto das meta-features que já são calculadas pelo PyMFE."*
- **Diagnóstico:** Na literatura de meta-aprendizagem, todas as características derivadas analiticamente (estatísticas, teoria da informação, complexidade) são denominadas *handcrafted*. A biblioteca PyMFE automatiza a extração de tais descritores handcrafted, mas não gera representações latentes aprendidas (como em *deep meta-learning*). Portanto, as 8 meta-features iniciais **não são uma categoria à parte** ("básicas" vs. "handcrafted") — são literalmente um subconjunto restrito (dos grupos `general`/`statistical`) do mesmo espaço handcrafted já coberto pelo PyMFE. A primeira correção aplicada (trocar "manuais" por "básicas") ainda mantinha essa dicotomia implícita e foi revisada.
- **Proposta de Resposta ao Professor:**
  > *"Agradecemos a precisão conceitual. Corrigimos a terminologia em todo o manuscrito (resumo, contribuições, metodologia e conclusão). Eliminamos por completo a dicotomia entre 'manuais'/'básicas' e 'handcrafted', deixando explícito que ambos os conjuntos pertencem à mesma família handcrafted e que a diferença é de abrangência: 'subconjunto handcrafted de 8 meta-features gerais e estatísticas' (Experimentos 1–3) versus 'conjunto handcrafted completo de 78 descritores do PyMFE, que engloba as 8 métricas anteriores como subconjunto' (Experimento 4). Essa relação de subconjunto/superconjunto é agora repetida em todos os pontos do texto em que os dois conjuntos são comparados, não apenas na Seção 3.2."*
- **Ação no Relatório (`relatorio-sbc.tex`):**
  - **Antes:**
    ```latex
    substituindo as 8 meta-features manuais por 78 descritores extraídos
    automaticamente pelo PyMFE...
    ```
  - **Depois (versão final aplicada):**
    ```latex
    \textbf{Experimento 4 (PyMFE):} os mesmos datasets do Experimento~3, agora
    caracterizados por 78 meta-features extraídas via \texttt{PyMFE}~\cite{pymfe2020}
    (grupos \texttt{general}, \texttt{statistical}, \texttt{info-theory} e \texttt{clustering}),
    que expandem as 8 métricas anteriores, com imputação por mediana e recorte de valores
    superiores a $10^{10}$.
    ```
  - Mesma lógica replicada no resumo, nas contribuições, na Seção 3.2 (Meta-features Extraídas) e na Conclusão, para evitar que a dicotomia reapareça em pontos isolados do texto.

---

### Comentário C6 — Nomenclatura dos Algoritmos Base
- **Localização:** Página 5, Seção 4.1 (Distribuição do Meta-Alvo), Título da Tabela 2.
- **Texto Destacado:** `"base"` (em *algoritmos base*)
- **Comentário do Professor:** *(Destaque indicativo).*
- **Diagnóstico:** O professor destacou a palavra "base". Esse destaque complementa o Comentário C4: a Tabela 2 descreve os algoritmos base e serve como a linha de base empírica para a avaliação prática. É necessário manter rígida consistência entre "algoritmo base" e "meta-modelo", além de conectar os valores médios da Tabela 2 com as baselines de avaliação (onde Random Forest com $0,734$ desponta como a melhor baseline estática).
- **Proposta de Resposta ao Professor:**
  > *"O destaque foi utilizado para assegurar consistência terminológica plena ao longo do texto, garantindo que o termo 'algoritmo base' seja sempre empregado para os 5 classificadores do problema primário e que seus escores na Tabela 2 sirvam de balizador direto para a avaliação em nível base solicitada no Comentário C4."*
- **Ação no Relatório (`relatorio-sbc.tex`):**
  - Amarrar a discussão da Tabela 2 com a avaliação em nível base da Seção 4.

---

## 4. Texto Proposto para Inserção no Relatório SBC (`relatorio-sbc.tex`)

### 4.1 Na Seção 2.4 (Métricas de Avaliação)

Adicionar após a descrição do Spearman e Acc@1:

```latex
\subsubsection{Avaliação em Nível Base}

Para avaliar o benefício prático entregue ao usuário do sistema de meta-aprendizagem, introduzimos uma avaliação em nível base. Seja $\mathbf{y}_i \in \mathbb{R}^M$ o vetor de F1-macro real dos $M$ algoritmos base no dataset de teste $i$, e seja $\hat{\mathbf{y}}_i$ a predição gerada pelo meta-modelo. O algoritmo recomendado em primeiro lugar no ranking é dado por $j^*(i) = \arg\max_{j} \hat{y}_{i, j}$. Avaliamos:

\begin{itemize}
  \item \textbf{Performance Média em Nível Base ($\overline{P}_{\text{base}}$):} Desempenho real médio obtido ao seguir a recomendação:
  \begin{equation}
    \overline{P}_{\text{base}} = \frac{1}{N} \sum_{i=1}^N y_{i, j^*(i)}
  \end{equation}
  \item \textbf{Perda em Relação ao Oráculo (\textit{Average Regret}):} Diferença entre o melhor classificador possível para cada dataset (Oráculo) e a escolha recomendada:
  \begin{equation}
    \overline{\text{Regret}} = \frac{1}{N} \sum_{i=1}^N \left( \max_{j} y_{i, j} - y_{i, j^*(i)} \right)
  \end{equation}
\end{itemize}

Essas métricas são comparadas contra duas referências estáticas: a \textit{Single Best} (adotar sempre o classificador com maior média histórica, Random Forest) e a \textit{Escolha Aleatória}.
```

### 4.2 Na Seção 4 (Resultados e Discussão)

Adicionar subseção com a tabela consolidada gerada a partir de `dados/avaliacao_nivel_base.csv`:

```latex
\subsection{Avaliação do Ganho Prático em Nível Base}

A Tabela~\ref{tab:base_level_eval} sintetiza a avaliação em nível base sobre os 198 datasets reais do Experimento~4 com descritores PyMFE. Os resultados evidenciam achados de alto impacto prático:

\begin{table}[H]
\centering
\caption{Avaliação em nível base: F1-macro médio do algoritmo recomendado (Top-1), Regret médio em relação ao Oráculo e ganho sobre a melhor baseline estática (Single Best).}
\label{tab:base_level_eval}
\small
\begin{tabular}{llcccc}
\toprule
\textbf{Meta-Modelo} & \textbf{Pré-processamento de $Y$} & \textbf{F1 Base ($\mu$)} & \textbf{Desv.\ Pad.} & \textbf{Regret ($\mu$)} & \textbf{Ganho vs.\ SB} \\
\midrule
\textit{Referência} & \textit{Oráculo Ideal} & \textit{0,7579} & \textit{0,1811} & \textit{0,0000} & \textit{+0,0256} \\
\textit{Referência} & \textit{Single Best (RF)} & \textit{0,7323} & \textit{0,1904} & \textit{0,0256} & \textit{0,0000} \\
\textit{Referência} & \textit{Escolha Aleatória} & \textit{0,6668} & \textit{0,2118} & \textit{0,0911} & \textit{-0,0655} \\
\midrule
\multirow{4}{*}{Ridge} 
 & Baseline / Transf.\ Lineares & 0,7214 & 0,1942 & 0,0365 & -0,0109 \\
 & QuantileTransformer & 0,7160 & 0,1914 & 0,0419 & -0,0163 \\
\midrule
\multirow{5}{*}{SVR} 
 & Baseline & 0,7296 & 0,1852 & 0,0283 & -0,0027 \\
 & StandardScaler & 0,7386 & 0,1848 & 0,0193 & +0,0063 \\
 & MinMaxScaler & 0,7332 & 0,1846 & 0,0247 & +0,0008 \\
 & \textbf{RobustScaler} & \textbf{0,7395} & 0,1841 & \textbf{0,0184} & \textbf{+0,0072} \\
 & QuantileTransformer & 0,7351 & 0,1865 & 0,0228 & +0,0028 \\
\midrule
\multirow{5}{*}{Random Forest} 
 & Baseline & 0,7372 & 0,1869 & 0,0207 & +0,0049 \\
 & StandardScaler & 0,7362 & 0,1868 & 0,0217 & +0,0039 \\
 & \textbf{MinMaxScaler} & \textbf{0,7381} & 0,1854 & \textbf{0,0198} & \textbf{+0,0058} \\
 & RobustScaler & 0,7372 & 0,1870 & 0,0207 & +0,0049 \\
 & QuantileTransformer & 0,7364 & 0,1850 & 0,0215 & +0,0041 \\
\midrule
\multirow{5}{*}{MLP} 
 & Baseline & 0,6576 & 0,2190 & 0,1003 & -0,0747 \\
 & MinMaxScaler & 0,6621 & 0,2131 & 0,0957 & -0,0702 \\
 & \textbf{StandardScaler} & \textbf{0,7062} & 0,2134 & \textbf{0,0517} & \textbf{-0,0261} \\
 & RobustScaler & 0,7028 & 0,2121 & 0,0551 & -0,0295 \\
 & QuantileTransformer & 0,7039 & 0,2044 & 0,0540 & -0,0285 \\
\bottomrule
\end{tabular}
\end{table}

A análise revela que a padronização e transformação do meta-alvo são decisivas para a viabilidade prática da meta-aprendizagem:
\begin{enumerate}
  \item O SVR sem transformação do meta-alvo (Baseline, $0,7296$) é inútil na prática, pois perde para a heurística trivial de sempre escolher Random Forest ($0,7323$). Com a introdução do RobustScaler, o SVR passa a superar a heurística ($0,7395$) e atinge o menor regret de todo o estudo ($0,0184$), ficando a menos de 2 pontos percentuais do Oráculo ideal.
  \item O MLP sem padronização do meta-alvo atinge um desastroso F1 de $0,6576$, operando abaixo até da escolha puramente aleatória ($0,6668$). A introdução de técnicas de padronização (StandardScaler e RobustScaler, que removem o offset positivo de bias) eleva a performance para $0,7062$, reduzindo o regret pela metade.
  \item O Random Forest confirma sua robustez intrínseca à escala do alvo, com performance estável em torno de $0,737$ a $0,738$ em todas as configurações.
\end{enumerate}
```

---

## 5. Checklist de Conclusão da Revisão

- [x] **Extração e análise dos comentários do PDF:** 6 anotações catalogadas e compreendidas.
- [x] **Cálculo da Avaliação em Nível Base:** Pipeline executado sobre os 198 datasets do Experimento 4 com dados salvos em [`dados/avaliacao_nivel_base.csv`](file:///c:/Users/lael_/OneDrive/Documentos/UFAL/Meta-aprendizagem/dados/avaliacao_nivel_base.csv).
- [x] **Revisão terminológica de meta-features (C5):** eliminada em definitivo a dicotomia "manuais/básicas vs. handcrafted" no resumo, contribuições, Seção 3.1 (Exp. 4), Seção 3.2 e Conclusão; ambos os conjuntos agora descritos como handcrafted, com a relação de subconjunto/superconjunto explicitada em cada ocorrência.
- [x] **Correção de erro de formatação:** `**RobustScaler**` (sintaxe Markdown) na Conclusão corrigido para `\textbf{RobustScaler}`.
- [x] **Criação do documento `RESPONSE.md`:** Estruturado e documentado.
- [x] **Aplicação no LaTeX (`relatorio-sbc.tex`):** Trechos revisados de texto, equações e tabelas inseridos e integrados com rigor conceitual.
- [x] **Compilação e Validação do PDF (`relatorio-sbc.pdf`):** Compilado com sucesso (exit code 0, 14 páginas, sem avisos de referências quebradas ou overfull hbox).
