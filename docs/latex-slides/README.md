# Apresentação — Slides em LaTeX Beamer

Esta pasta contém os arquivos fonte em LaTeX para a geração dos slides de apresentação do projeto (15 minutos).

## Estrutura da Pasta

- [slides.tex](file:///c:/Users/lael_/OneDrive/Documentos/UFAL/Meta-aprendizagem/docs/latex-slides/slides.tex): Arquivo fonte principal dos slides LaTeX Beamer.
- [figures/](file:///c:/Users/lael_/OneDrive/Documentos/UFAL/Meta-aprendizagem/docs/latex-slides/figures/): Contém os gráficos utilizados na apresentação.
  - [comparativo_pymfe.png](file:///c:/Users/lael_/OneDrive/Documentos/UFAL/Meta-aprendizagem/docs/latex-slides/figures/comparativo_pymfe.png): Novo gráfico comparativo de resultados do Experimento 4 (gerado automaticamente com base nos dados reais).
- [notes.md](file:///c:/Users/lael_/OneDrive/Documentos/UFAL/Meta-aprendizagem/docs/latex-slides/notes.md): Roteiro de fala detalhado (diálogos), tempos estimados, comentários e links de fácil navegação em formato Markdown.

---

## Pré-requisitos de Compilação

Para compilar os slides, certifique-se de que possui uma distribuição LaTeX instalada (TeX Live, MiKTeX ou MacTeX) com suporte aos seguintes pacotes estándar:
- `beamer` (classe principal)
- `inputenc` e `babel` (com suporte a `brazilian`)
- `graphicx` e `booktabs`
- `tikz` (com bibliotecas `shapes.geometric`, `arrows.meta`, `positioning`, `fit`, `backgrounds`)
- `colortbl`, `array` e `multirow`

---

## Instruções de Compilação

### Opção 1: Compilador Online (Overleaf)
1. Crie um novo projeto no [Overleaf](https://www.overleaf.com).
2. Faça upload de todos os arquivos desta pasta (mantendo a pasta `figures/` com a imagem).
3. Selecione o compilador padrão `pdfLaTeX` nas opções do Overleaf.
4. Clique em **Recompile**.

### Opção 2: Linha de Comando (Local)
Abra o terminal na pasta `docs/latex-slides/` e execute o comando `pdflatex` duas vezes para resolver referências cruzadas e desenhos do TikZ:

```bash
pdflatex slides.tex
pdflatex slides.tex
```

Se você usa a ferramenta `latexmk` (recomendado para automação), basta executar:

```bash
latexmk -pdf slides.tex
```

Isso gerará o arquivo `slides.pdf` pronto na mesma pasta.
