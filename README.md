# Análise de Risco de Crédito — Default of Credit Card Clients

Trabalho Final Prático — Python & Pandas
Disciplina: Novas Tecnologias / Ciência de Dados | Prof. William Malvezzi
Grupo 03 — Tema 03 (Finanças e Risco de Crédito)

## 1. Sobre este documento

Este README descreve como executar o notebook `AnaliseCredito.ipynb` e documenta as revisões técnicas aplicadas à versão original do grupo para adequação aos requisitos do trabalho. As revisões foram feitas em duas rodadas, descritas nas Seções 4 e 5.

## 2. Como executar o notebook

1. Abrir o arquivo `AnaliseCredito.ipynb` no Google Colab.
2. Executar as células em ordem, de cima para baixo (`Ambiente de execução → Executar tudo`). O notebook não depende de upload manual de arquivo: a base é carregada diretamente do UCI Machine Learning Repository por meio da biblioteca `ucimlrepo` (`fetch_ucirepo(id=350)`), na célula do Passo 1.
3. Não é necessário nenhum arquivo local adicional. A primeira célula de código instala a dependência `ucimlrepo` via `pip`.
4. Tempo estimado de execução completa: poucos minutos, já que a base tem 30.000 linhas e nenhuma etapa do notebook depende de treinamento de modelo.

### Bibliotecas e versões utilizadas

| Biblioteca | Versão | Uso no notebook |
|---|---|---|
| pandas | 2.2.2 | Manipulação e análise de dados tabulares |
| numpy | 2.0.2 | Operações matemáticas e lógica condicional (`np.where`, `np.select`) |
| matplotlib | 3.x | Geração de gráficos |
| seaborn | 0.13.x | Gráficos estatísticos (heatmap de correlação) |
| ucimlrepo | 0.0.7 | Carregamento direto da base a partir do UCI ML Repository |

As versões correspondem ao ambiente padrão do Google Colab no momento do desenvolvimento. A célula de configuração inicial do notebook imprime a versão de `pandas` e `numpy` efetivamente carregada em tempo de execução, permitindo conferência a qualquer momento.

## 3. Estrutura do notebook

O notebook segue a estrutura de passos definida no tutorial da disciplina:

- Passo 0 — Configuração do ambiente e bibliotecas
- Passo 1 — Carregamento da base de dados
- Passo 2 — Compreensão inicial dos dados
- Passo 3 — Dicionário de dados (tabela técnica)
- Estatística descritiva aprofundada (média, mediana, desvio padrão, assimetria)
- As 8 perguntas de análise exploratória, cada uma sob responsabilidade de um integrante do grupo (ver capa do relatório), com tratamento de dados, criação de variáveis, gráficos e interpretação textual associados
- Indicadores sintéticos e conclusões

## 4. Revisão técnica — primeira rodada

Nesta etapa, duas seções específicas do notebook foram revisadas a pedido do grupo: a Pergunta 1 (Score de Risco) e a Pergunta 5 (Outliers em BILL_AMT com IQR). Em ambas, a lógica de código já estava correta e foi validada por execução, mas faltavam elementos exigidos pelo enunciado do trabalho.

**Pergunta 1 — Score de Risco**
- Adicionada célula de explicação conceitual ("o que vamos fazer e por quê") antes do bloco de código, conforme exigido para todas as seções do notebook.
- Adicionado histograma da distribuição do score de risco, com média e mediana demarcadas.
- Adicionado gráfico de dispersão (scatter) relacionando quantidade de atrasos e score de risco.
- Adicionada validação cruzada entre a categoria de risco criada e a inadimplência real (`DEFAULT`), confirmando que o score proposto discrimina risco de forma consistente.
- Adicionadas interpretações textuais para cada novo resultado.

**Pergunta 5 — Outliers em BILL_AMT**
- Adicionada célula de explicação conceitual antes do código original.
- Adicionado box plot e histograma de `BILL_AMT1` com os limites do método IQR demarcados.
- Estendida a aplicação do método IQR a uma segunda coluna numérica, `BILL_AMT3`, atendendo ao requisito de detecção de outliers em pelo menos duas variáveis. Foi incluído um box plot comparativo entre as duas colunas e a respectiva interpretação.
- Atualizada a conclusão da seção para incorporar o resultado da segunda coluna analisada.

Essa primeira rodada também teve como efeito colateral positivo cobrir, em todo o notebook, dois tipos de gráfico que ainda não apareciam em nenhuma outra seção do trabalho — histograma e gráfico de dispersão —, completando a exigência dos sete tipos de gráfico obrigatórios (barras, histograma, dispersão, linha, box plot, mapa de calor e gráfico de pizza/barras empilhadas).

## 5. Revisão técnica — segunda rodada

Após a primeira rodada, foi feita uma auditoria de cobertura comparando o conteúdo do notebook com a lista completa de técnicas obrigatórias de Pandas exigidas pelo enunciado do trabalho (Seção 2.1 do documento de instruções). Foram identificadas sete técnicas previstas no enunciado que ainda não constavam em nenhuma célula do notebook. Todas foram implementadas nesta rodada, descritas a seguir.

**Dicionário de dados técnico.** Inserido logo após o Passo 2, complementando a tabela semântica já existente no início do notebook (coluna, tradução e significado). A nova tabela é gerada por código e apresenta, para cada uma das 25 colunas originais, o tipo de dado, a quantidade e o percentual de valores nulos, a quantidade de valores únicos e um exemplo real de valor. O resultado confirma a ausência de valores ausentes em toda a base.

**Estatística descritiva aprofundada.** Inserida logo após o dicionário técnico. Foram calculadas média, mediana, desvio padrão e coeficiente de assimetria (skewness) para as variáveis `LIMIT_BAL` e `AGE`, com interpretação textual da diferença de comportamento entre uma variável monetária (fortemente assimétrica) e uma variável demográfica (distribuição mais regular).

**Padronização de texto (`str.strip()` / `str.upper()`).** Inserida na Pergunta 2, logo após o mapeamento dos códigos numéricos de `SEX`, `EDUCATION` e `MARRIAGE` para rótulos textuais. A padronização evita que variações de espaçamento ou capitalização sejam tratadas como categorias distintas em operações de agrupamento.

**Verificação de duplicatas (`drop_duplicates()`).** Inserida na sequência da padronização de texto. A verificação mostrou que não há linhas idênticas considerando todas as colunas, incluindo o identificador único (`ID`). Ao desconsiderar o `ID` e comparar apenas o perfil financeiro completo, foram encontrados 35 clientes com perfil idêntico a outro cliente da base — resultado documentado e mantido na análise, com justificativa estatística, em vez de removido.

**Tabela cruzada (`pd.crosstab()`).** Inserida na Pergunta 2, na sequência da tabela pivô já existente. Cruza Sexo e Estado Civil em relação à condição de adimplência, complementando a leitura agregada da tabela pivô com a distribuição percentual completa por grupo.

**Agrupamento multinível (`groupby` por duas ou mais colunas, com `.agg()` multimétrica).** Inserido na sequência do item anterior, agrupando simultaneamente por Sexo, Escolaridade e Estado Civil, com cálculo de total de clientes, taxa de inadimplência e limite de crédito médio por combinação. Combinações com menos de 50 clientes foram descartadas do ranking, evitando que grupos muito pequenos produzissem taxas estatisticamente instáveis.

**Conversão de datas (`pd.to_datetime()`).** Inserida na Pergunta 8, antes da geração do gráfico de linha sobre evolução temporal da inadimplência. Como a base não possui uma coluna de data nativa — o período de cada coluna de pagamento está embutido no nome da coluna (`PAY_0` a `PAY_6`, referentes a abril a setembro de 2005) —, foi construída uma tabela de datas reais a partir dessa correspondência, com extração do nome do mês via `.dt.strftime()`. O gráfico de linha da Pergunta 8 passou a usar esses rótulos derivados da coluna de data, em vez de uma lista de texto fixa digitada manualmente.

### Ajuste de compatibilidade decorrente da padronização de texto

A introdução de `str.upper()` nas colunas categóricas alterou a capitalização dos valores de `EDUCATION` e `MARRIAGE` (por exemplo, de `"Pós-graduação"` para `"PÓS-GRADUAÇÃO"`). Duas células de código já existentes no notebook, responsáveis pela ordenação de categorias nos gráficos das seções 1.4 e 1.5, utilizavam listas de referência com a capitalização original e deixaram de encontrar correspondência após a padronização. As listas de referência (`ordem` e `ordem_civ`) foram atualizadas para o mesmo padrão de capitalização, eliminando o erro sem alterar a lógica original dessas células.

### Validação de execução

Após cada rodada de revisão, o notebook foi executado integralmente do início ao fim (equivalente a `Ambiente de execução → Executar tudo`), confirmando ausência de erros e de avisos (*warnings*) em qualquer célula. Como o ambiente de revisão não possui acesso à mesma rede do Google Colab, a validação foi feita substituindo, de forma temporária e apenas para fins de teste, a chamada `fetch_ucirepo(id=350)` por uma cópia local da mesma base de dados (arquivo oficial do UCI), reconstruída com a estrutura idêntica à retornada pela biblioteca `ucimlrepo` (colunas `X1` a `X23` e `Y`, incluindo `ID`). Essa substituição não está presente na versão final entregue, que mantém a chamada original à API do UCI.

## 6. Cobertura final de requisitos técnicos

Após as duas rodadas de revisão, o notebook contempla a totalidade das técnicas de Pandas e dos tipos de gráfico exigidos pelo enunciado do trabalho:

- 15 de 15 técnicas obrigatórias de Pandas (Seção 2.1 do enunciado).
- 7 de 7 tipos de gráfico obrigatórios (Seção 2.2 do enunciado): barras, histograma, dispersão, linha, box plot, mapa de calor e pizza/barras empilhadas.
- 7 de 7 requisitos avançados pontuados (Seção 05 do enunciado): outliers por IQR em duas ou mais colunas, tabela pivô com duas ou mais dimensões, mapa de calor com interpretação das correlações mais fortes, box plot com interpretação de mediana e outliers, `apply` com função lambda não trivial, agrupamento multinível com agregação multimétrica, e interpretação estatística formal (média, mediana, desvio padrão e assimetria) para duas ou mais variáveis.

## 7. Integrantes e divisão de responsabilidades

| Integrante | Pergunta(s) sob responsabilidade |
|---|---|
| Ana Beatriz Gonçalves Bastos | Pergunta 1 — Score de risco · Pergunta 5 — Outliers IQR (BILL_AMT) |
| Matheus Martins Rios | Pergunta 2 — Taxa de inadimplência por perfil |
| Gustavo Xavier | Pergunta 3 — Comportamento de pagamento da fatura |
| Yuri | Pergunta 4 — Matriz de correlação |
| Matheus Henrique | Pergunta 6 — Segmentação de perfis (clustering) |
| Otávio Henrique do Nascimento Cunha | Pergunta 7 — Ranking de limite · Pergunta 8 — Piora progressiva |

## 8. Referências

- UCI Machine Learning Repository. Disponível em: archive.ics.uci.edu.
- McKINNEY, Wes. *Python for Data Analysis*. 3. ed. O'Reilly, 2022.
- Documentação oficial do Pandas. Disponível em: pandas.pydata.org/docs.
