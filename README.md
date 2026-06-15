# Plano Ágil - Projeto Final de Meta-aprendizagem

**Tema 3:** O efeito de técnicas de escalonamento (normalização) aplicadas às variáveis dependentes em sistemas de meta-aprendizagem: Estudar o efeito de técnicas de escalonamento aplicadas ao meta-alvo (às performances dos algoritmos de nível base) na performance do meta-modelo.

- Existe diferença relevante?
- Há uma recomendação geral que funcione para qualquer meta-modelo?
- Ver artigo “The choice of scaling technique matters for classification performance”

---

## 🎯 Sprint 1: Fundamentação, Planejamento e Definição de Dados

- [X] **Leitura do artigo base:** "The choice of scaling technique matters for classification performance".
- [X] **Escrita do Relatório (Introdução):**
  - [X] Escrever o contexto da pesquisa e do tema 3.
  - [X] Definir claramente o problema e os objetivos.
  - [X] Estabelecer as perguntas de pesquisa/hipóteses.
- [X] **Escrita do Relatório (Proposta):**
  - [X] Explicar a proposta teoricamente, independente dos experimentos (com diagramas ou fórmulas se aplicável).
- [X] **Definição da Base de Dados:** Selecionar/coletar bases de dados públicas (OpenML) e salvar uma cópia no projeto.
- [X] **Definição dos Algoritmos Base e Meta-alvo:** Definir quais algoritmos serão avaliados (ex: SVM, Random Forest) e qual métrica será o meta-alvo (ex: acurácia, F1-score).
- [X] **Definição das Técnicas de Escalonamento:** Escolher as técnicas (ex: Min-Max, Z-score, Robust Scaler) para aplicar no meta-alvo.
- [X] **Definição do Meta-modelo:** Selecionar o(s) algoritmo(s) do nível meta.
- [X] **Estruturação do Repositório:** Organizar as pastas (`/dados`, `/src` ou `/notebooks`, `/relatorio`, `/apresentacao`).

## 🛠️ Sprint 2: Implementação e Coleta de Meta-dados (Metodologia)

- [X] **Extração de Meta-features:**
  - [X] Implementar script para extrair as características das bases de dados.
- [X] **Avaliação em Nível Base:**
  - [X] Treinar os algoritmos base e extrair seus desempenhos para formar o meta-alvo.
- [X] **Implementação das Técnicas de Escalonamento:**
  - [X] Aplicar as técnicas de normalização diretamente no meta-alvo.
- [X] **Desenvolvimento do Meta-modelo:**
  - [X] Montar o pipeline com validação cruzada.
- [X] **Escrita do Relatório (Metodologia Experimental):**
  - [X] Detalhar no relatório os dados utilizados.
  - [X] Descrever os algoritmos de nível base, meta-modelos e hiperparâmetros.
  - [X] Documentar a validação cruzada e as métricas avaliadas.

## 🔬 Sprint 3: Experimentos, Resultados e Discussão

- [X] **Execução dos Experimentos:**
  - [X] Treinar/Testar o meta-modelo com o baseline (meta-alvo sem normalização).
  - [X] Treinar/Testar o meta-modelo com as diferentes técnicas de escalonamento.
- [X] **Coleta e Análise de Resultados:**
  - [X] Consolidar as métricas de performance do meta-modelo.
  - [X] Gerar os gráficos principais (boxplots, barras).
  - [X] **Testes de Hipóteses:** Aplicar testes estatísticos para verificar se há "diferença relevante".
- [X] **Escrita do Relatório (Resultados e Discussão):**
  - [X] Inserir gráficos e tabelas no documento.
  - [X] Analisar os resultados e discutir o impacto do escalonamento (responder: "Há uma recomendação geral?").
- [X] **Escrita do Relatório (Conclusão e Revisão Final):**
  - [X] Resumir os achados finais e os principais resultados que respondem ao objetivo original (ajustar a Introdução se necessário).
  - [X] Revisar gramática, formatação e enquadramento nas 4 a 6 páginas.

## 📊 Sprint 4: Apresentação e Entrega

- [X] **Preparação dos Slides:**
  - [X] Montar a apresentação abordando os tópicos do relatório sintetizados.
  - [X] Ensaiar para garantir que o tempo fique dentro dos **15 minutos**.
- [X] **Revisão da Entrega Final:**
  - [X] Código fonte: Scripts, notebooks, todos bem documentados.
  - [X] Dados: Arquivos disponíveis na pasta ou em link público referenciado.
  - [X] Documentos: Relatório e apresentação criados em LaTex e salvos em formato PDF.
- [X] **Submissão Final!**
