# Plano Ágil - Projeto Final de Meta-aprendizagem

**Tema 3:** O efeito de técnicas de escalonamento (normalização) aplicadas às variáveis dependentes em sistemas de meta-aprendizagem: Estudar o efeito de técnicas de escalonamento aplicadas ao meta-alvo (às performances dos algoritmos de nível base) na performance do meta-modelo.

- Existe diferença relevante?
- Há uma recomendação geral que funcione para qualquer meta-modelo?
- Ver artigo “The choice of scaling technique matters for classification performance”

---

## 🎯 Sprint 1: Fundamentação, Planejamento e Definição de Dados

- [x] **Leitura do artigo base:** "The choice of scaling technique matters for classification performance".
- [x] **Escrita do Relatório (Introdução):**
  - [x] Escrever o contexto da pesquisa e do tema 3.
  - [x] Definir claramente o problema e os objetivos.
  - [x] Estabelecer as perguntas de pesquisa/hipóteses.
- [x] **Escrita do Relatório (Proposta):**
  - [x] Explicar a proposta teoricamente, independente dos experimentos (com diagramas ou fórmulas se aplicável).
- [x] **Definição da Base de Dados:** Selecionar/coletar bases de dados públicas (OpenML) e salvar uma cópia no projeto.
- [x] **Definição dos Algoritmos Base e Meta-alvo:** Definir quais algoritmos serão avaliados (ex: SVM, Random Forest) e qual métrica será o meta-alvo (ex: acurácia, F1-score).
- [x] **Definição das Técnicas de Escalonamento:** Escolher as técnicas (ex: Min-Max, Z-score, Robust Scaler) para aplicar no meta-alvo.
- [x] **Definição do Meta-modelo:** Selecionar o(s) algoritmo(s) do nível meta.
- [x] **Estruturação do Repositório:** Organizar as pastas (`/dados`, `/src` ou `/notebooks`, `/relatorio`, `/apresentacao`).

## 🛠️ Sprint 2: Implementação e Coleta de Meta-dados (Metodologia)

- [x] **Extração de Meta-features:**
  - [x] Implementar script para extrair as características das bases de dados.
- [x] **Avaliação em Nível Base:**
  - [x] Treinar os algoritmos base e extrair seus desempenhos para formar o meta-alvo.
- [x] **Implementação das Técnicas de Escalonamento:**
  - [x] Aplicar as técnicas de normalização diretamente no meta-alvo.
- [x] **Desenvolvimento do Meta-modelo:**
  - [x] Montar o pipeline com validação cruzada.
- [x] **Escrita do Relatório (Metodologia Experimental):**
  - [x] Detalhar no relatório os dados utilizados.
  - [x] Descrever os algoritmos de nível base, meta-modelos e hiperparâmetros.
  - [x] Documentar a validação cruzada e as métricas avaliadas.

## 🔬 Sprint 3: Experimentos, Resultados e Discussão

- [x] **Execução dos Experimentos:**
  - [x] Treinar/Testar o meta-modelo com o baseline (meta-alvo sem normalização).
  - [x] Treinar/Testar o meta-modelo com as diferentes técnicas de escalonamento.
- [x] **Coleta e Análise de Resultados:**
  - [x] Consolidar as métricas de performance do meta-modelo.
  - [x] Gerar os gráficos principais (boxplots, barras).
  - [x] **Testes de Hipóteses:** Aplicar testes estatísticos para verificar se há "diferença relevante".
- [x] **Escrita do Relatório (Resultados e Discussão):**
  - [x] Inserir gráficos e tabelas no documento.
  - [x] Analisar os resultados e discutir o impacto do escalonamento (responder: "Há uma recomendação geral?").
- [x] **Escrita do Relatório (Conclusão e Revisão Final):**
  - [x] Resumir os achados finais e os principais resultados que respondem ao objetivo original (ajustar a Introdução se necessário).
  - [x] Revisar gramática, formatação e enquadramento nas 4 a 6 páginas.

## 📊 Sprint 4: Apresentação e Entrega

- [ ] **Preparação dos Slides:**
  - [ ] Montar a apresentação abordando os tópicos do relatório sintetizados.
  - [ ] Ensaiar para garantir que o tempo fique dentro dos **30 minutos**.
  - [ ] Dividir para garantir que **todos os integrantes participem**.
- [ ] **Revisão da Entrega Final:**
  - [ ] Código fonte: Scripts, notebooks, todos bem documentados.
  - [ ] Dados: Arquivos disponíveis na pasta ou em link público referenciado.
  - [ ] Documentos: Relatório e apresentação salvos em formato PDF.
- [ ] **Submissão Final!**
