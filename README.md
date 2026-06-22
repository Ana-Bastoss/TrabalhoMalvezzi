# Análise de Risco de Crédito — Default of Credit Card Clients

Trabalho Final Prático — Python & Pandas
Disciplina: Novas Tecnologias / Ciência de Dados | Prof. William Malvezzi
Grupo 03 — Tema 03 (Finanças e Risco de Crédito)

[VÍDEO NO YOUTUBE] (https://youtu.be/ghIW78122p8)

## 1. Sobre o projeto

Este projeto analisa a base *Default of Credit Card Clients*, do UCI Machine Learning Repository, com 30.000 clientes de cartão de crédito de Taiwan (2005). O objetivo é identificar quais características financeiras e comportamentais estão associadas à inadimplência no mês seguinte, por meio de oito perguntas de análise distribuídas entre os integrantes do grupo.

O projeto é composto por quatro entregas: o notebook (`AnaliseCredito.ipynb`), a base de dados, o relatório em PDF e a apresentação em slides. Este README documenta o notebook.

## 2. Integrantes e divisão de responsabilidades

| Integrante | Pergunta(s) sob responsabilidade |
|---|---|
| Ana Beatriz Gonçalves Bastos | Pergunta 1 — Score de risco · Pergunta 5 — Outliers IQR (BILL_AMT) |
| Matheus Martins Rios | Pergunta 2 — Taxa de inadimplência por perfil |
| Gustavo Xavier | Pergunta 3 — Comportamento de pagamento da fatura |
| Yuri | Pergunta 4 — Matriz de correlação |
| Matheus Henrique | Pergunta 6 — Segmentação de perfis (clustering) |
| Otávio Henrique do Nascimento Cunha | Pergunta 7 — Ranking de limite · Pergunta 8 — Piora progressiva |

## 3. Como executar o notebook

1. Abrir `AnaliseCredito.ipynb` no Google Colab.
2. Executar as células em ordem, do início ao fim (`Ambiente de execução → Executar tudo`).
3. Não é necessário upload manual de arquivo: a base é carregada diretamente do UCI Machine Learning Repository pela biblioteca `ucimlrepo` (`fetch_ucirepo(id=350)`), na célula do Passo 1. A primeira célula de código instala essa dependência via `pip`.
4. Tempo estimado de execução completa: poucos minutos. Nenhuma etapa do notebook depende de treinamento de modelo.

### Bibliotecas utilizadas

| Biblioteca | Versão | Uso no notebook |
|---|---|---|
| pandas | 2.2.2 | Leitura, limpeza, transformação e agregação dos dados (`groupby`, `pivot_table`, `crosstab`, `cut`, etc.) |
| numpy | 2.0.2 | Operações matemáticas e lógica condicional vetorizada (`where`, `select`) |
| matplotlib | 3.x | Geração de todos os gráficos do notebook (barras, histograma, dispersão, linha, box plot) |
| seaborn | 0.13.x | Gráficos estatísticos, em especial o mapa de calor de correlação |
| ucimlrepo | 0.0.7 | Carregamento direto da base a partir do UCI ML Repository, sem necessidade de download manual |

As versões correspondem ao ambiente padrão do Google Colab no momento do desenvolvimento. A célula de configuração inicial do notebook imprime a versão de `pandas` e `numpy` efetivamente carregada, permitindo conferência a qualquer momento.

## 4. Estrutura do notebook

O notebook segue a sequência de passos definida no tutorial da disciplina:

- **Passo 0** — Configuração do ambiente e bibliotecas
- **Passo 1** — Carregamento da base de dados
- **Passo 2** — Compreensão inicial dos dados
- **Passo 3** — Dicionário de dados (tabela técnica) e estatística descritiva aprofundada (média, mediana, desvio padrão, assimetria)
- **Passo 4 a 7** — Limpeza, tratamento e criação de variáveis, desenvolvidos dentro de cada uma das 8 perguntas de análise
- **As 8 perguntas de análise exploratória**, cada uma sob responsabilidade de um integrante do grupo, com tratamento de dados, criação de variáveis derivadas, gráficos e interpretação textual
- **Indicadores sintéticos e conclusões finais**

## 5. Problemas encontrados e correções aplicadas

Durante o desenvolvimento colaborativo do notebook, com várias pessoas editando seções diferentes, surgiram alguns problemas de execução e de cobertura de requisitos. Esta seção documenta o que foi encontrado e como foi corrigido.

### 5.1 Erros de execução por recarregamento inconsistente do `df`

Como cada integrante trabalhava em uma pergunta diferente, o DataFrame principal (`df`) era recarregado e renomeado várias vezes ao longo do notebook, o que gerava erros quando uma célula dependia de um nome de coluna que outra célula havia alterado ou revertido. Os erros mais comuns foram:

- `KeyError: 'DEFAULT'` — uma célula nomeava a coluna-alvo como `default_payment`, enquanto outra esperava `DEFAULT`.
- `KeyError: 'BILL_AMT1'` — uma célula redefinia `df` a partir de `base.data.features.copy()`, revertendo as colunas para os nomes originais (`X1`, `X12` etc.) e fazendo com que os nomes legíveis deixassem de existir.
- `KeyError: 'LIMIT_BAL'` — mesma causa do item anterior, em outra pergunta.
- Colunas de atraso referenciadas de forma inconsistente: parte do código usava `PAY_1`, que não existe no dataset (a sequência real é `PAY_0, PAY_2, PAY_3...`).

**Correção:** foi adotada a função `copia_base_com_colunas_nomeadas(base)`, chamada no início de cada pergunta para recarregar o `df` já com os nomes de coluna padronizados, evitando que o trabalho de uma pergunta interferisse na seguinte. A lista oficial de colunas de atraso (`['PAY_0','PAY_2','PAY_3','PAY_4','PAY_5','PAY_6']`) foi padronizada e usada de forma consistente em todas as seções que dependem dela.

### 5.2 Lacunas de cobertura em relação aos requisitos do trabalho

Uma auditoria do notebook contra a lista de técnicas e gráficos obrigatórios do enunciado identificou itens previstos que ainda não apareciam em nenhuma célula. Todos foram implementados:

- **Dicionário de dados técnico** (tipo, nulos, percentual de nulos, valores únicos e exemplo por coluna), inserido logo após a etapa de compreensão inicial dos dados. O resultado confirmou ausência de valores nulos em toda a base.
- **Estatística descritiva aprofundada** (média, mediana, desvio padrão e assimetria) para as variáveis `LIMIT_BAL` e `AGE`, com interpretação da diferença entre uma variável monetária (fortemente assimétrica) e uma variável demográfica (distribuição mais regular).
- **Padronização de texto** (`str.strip()` / `str.upper()`) aplicada às colunas `SEX`, `EDUCATION` e `MARRIAGE` após sua conversão de código numérico para rótulo textual.
- **Verificação de duplicatas** (`drop_duplicates()`): não há linhas idênticas considerando todas as colunas (o `ID` é único por definição). Ao desconsiderar o `ID` e comparar apenas o perfil financeiro completo, foram encontrados 35 clientes com perfil idêntico ao de outro cliente da base — resultado mantido na análise, com a justificativa de que coincidências desse tipo são estatisticamente esperadas em uma base de 30.000 linhas com variáveis discretas de baixa cardinalidade, e não indicam erro de coleta.
- **Tabela cruzada** (`pd.crosstab()`) de Sexo × Estado Civil, complementando a tabela pivô de Sexo × Escolaridade já existente.
- **Agrupamento multinível** (`groupby` por Sexo, Escolaridade e Estado Civil simultaneamente, com `.agg()` calculando total de clientes, taxa de inadimplência e limite de crédito médio por combinação), descartando grupos com menos de 50 clientes para evitar taxas estatisticamente instáveis.
- **Conversão de datas** (`pd.to_datetime()`): como a base não possui coluna de data nativa — o período de cada coluna de pagamento está embutido no nome da coluna (`PAY_0` a `PAY_6`, referentes a abril–setembro de 2005) —, foi construída uma tabela de datas reais a partir dessa correspondência, usada para gerar os rótulos do eixo X do gráfico de evolução temporal da Pergunta 8, em vez de uma lista de texto fixa.
- **Histograma e gráfico de dispersão**, os únicos dois tipos de gráfico obrigatórios que ainda não apareciam em nenhuma seção do notebook, foram incluídos nas Perguntas 1 (score de risco) e 5 (outliers), completando a exigência dos sete tipos de gráfico do trabalho.
- **Detecção de outliers em uma segunda coluna numérica**: a Pergunta 5 originalmente aplicava o método IQR apenas em `BILL_AMT1`; a análise foi estendida a `BILL_AMT3`, atendendo ao requisito de aplicar a técnica em pelo menos duas variáveis.

### 5.3 Efeito colateral da padronização de texto

A introdução de `str.upper()` nas colunas categóricas alterou a capitalização dos valores (por exemplo, de `"Pós-graduação"` para `"PÓS-GRADUAÇÃO"`). Duas células do notebook, responsáveis por ordenar categorias nos gráficos de inadimplência por escolaridade e por estado civil, usavam listas de referência com a capitalização original e deixaram de encontrar correspondência depois da padronização. As listas de referência foram atualizadas para o mesmo padrão de capitalização, eliminando o erro sem alterar a lógica original dessas células.

### 5.4 Validação final

Após as correções, o notebook foi executado integralmente do início ao fim (`Ambiente de execução → Executar tudo`), confirmando ausência de erros e de avisos em qualquer célula, com todas as 25 colunas, gráficos e tabelas sendo gerados corretamente em sequência.

## 6. Cobertura de requisitos técnicos

Ao final das correções, o notebook contempla a totalidade das exigências técnicas do enunciado:

- 15 de 15 técnicas obrigatórias de Pandas (leitura de dados, inspeção, tratamento de nulos e duplicatas, padronização de texto, conversão de datas, `groupby`/`agg`, `crosstab`, `np.where`/`np.select`, `cut`/`qcut`, `pivot_table`, `corr()`, IQR manual, `apply` com lambda, e agrupamento multinível).
- 7 de 7 tipos de gráfico obrigatórios: barras, histograma, dispersão, linha, box plot, mapa de calor e pizza/barras empilhadas.
- 7 de 7 requisitos avançados pontuados: outliers por IQR em duas ou mais colunas, tabela pivô com duas ou mais dimensões, mapa de calor com interpretação das correlações mais fortes, box plot com interpretação de mediana e outliers, `apply` com lambda em lógica não trivial, agrupamento multinível com agregação multimétrica, e interpretação estatística formal de média, mediana, desvio padrão e assimetria.

## 7. Referências


- UCI Machine Learning Repository. Disponível em: archive.ics.uci.edu.
- McKINNEY, Wes. *Python for Data Analysis*. 3. ed. O'Reilly, 2022.
- O que é risco de Crédito? Serasa - https://www.serasa.com.br/credito/blog/entenda-o-que-e-risco-de-credito-e-por-que-ele-deve-ser-calculado/
- Documentação oficial do Pandas. Disponível em: pandas.pydata.org/docs.
