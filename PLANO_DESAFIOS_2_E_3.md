# Plano de trabalho · Desafios 2 e 3 · Serviço Meteorológico

Este arquivo explica, passo a passo, **o que falta fazer** e **o que precisa ser ajustado** nos Desafios 2 (Python) e 3 (Excel), sempre com o tema **Serviço Meteorológico** e as tabelas `estacoes` e `leituras`.

---

## Sumário

1. [Situação atual](#1-situação-atual)
2. [A base do tema em 1 minuto](#2-a-base-do-tema-em-1-minuto)
3. [Escolhas da análise (valem para os dois desafios)](#3-escolhas-da-análise-valem-para-os-dois-desafios)
4. [Desafio 2 · Estatística com Python](#4-desafio-2--estatística-com-python)
5. [Desafio 3 · Relatório dinâmico no Excel](#5-desafio-3--relatório-dinâmico-no-excel)
6. [Entrega no GitHub](#6-entrega-no-github)
7. [Lista final de conferência](#7-lista-final-de-conferência)

---

## 1. Situação atual

| Desafio | Arquivo | Situação |
| --- | --- | --- |
| 1 · Python, API | `exercicios/Desafio1_API_Python.ipynb` | **Concluído.** As 72.050 linhas (10.050 estações + 62.000 leituras) foram enviadas para a planilha Google e conferidas |
| 2 · Estatística | `exercicios/Desafio2_Estatistica_Python.ipynb` | **Pela metade.** As células 01 a 08 e a 11 já rodaram. A célula 09 ainda está com a lacuna, e por isso a célula 10 deu erro. Faltam as células 12 a 20 e quase todos os textos |
| 3 · Excel | `exercicios/Desafio3_Excel_Modelo Copiar.xlsx` | **Não começado.** É o modelo em branco: as abas existem, mas não há dados, tabelas, fórmulas nem tabela dinâmica |

---

## 2. A base do tema em 1 minuto

| Conceito | No Serviço Meteorológico |
| --- | --- |
| **Tabela dimensão** (cadastro) | `estacoes`: cada linha é uma estação meteorológica (10.050 linhas, 7 colunas) |
| **Tabela fato** (eventos) | `leituras`: cada linha é uma leitura do tempo feita por uma estação (62.000 linhas, 9 colunas) |
| **Chave primária da dimensão** | `estacao_id` em `estacoes` |
| **Chave primária da fato** | `leitura_id` em `leituras` |
| **Chave estrangeira** | `estacao_id` em `leituras`, que aponta para `estacao_id` em `estacoes` |
| **Relação** | Uma estação tem várias leituras (de 1 a 16); cada leitura pertence a uma única estação |

**Colunas numéricas da fato:** `temperatura_c`, `umidade_pct`, `precipitacao_mm`, `velocidade_vento_kmh`, `pressao_hpa`.
**Colunas categóricas:** `condicao_tempo` (fato, 4 valores) e `pais` (dimensão, 15 valores).
**Coluna de data:** `data_hora` (fato), de 01/01/2025 a 31/07/2026.

Características da base que influenciam a análise:

- **Não há valores ausentes** nem linhas duplicadas, em nenhuma das duas tabelas.
- A `condicao_tempo` é sorteada primeiro e "puxa" as outras variáveis: tempestade tem mais vento, mais umidade e menos pressão.
- A **pressão depende quase toda da altitude** da estação, por isso ela é uma má escolha para comparar condições do tempo.
- A **chuva (`precipitacao_mm`) é zero em cerca de 88% das leituras**: ela só existe nas leituras `chuvoso` e `tempestade`.
- A **temperatura varia muito por latitude, altitude e época do ano**, então a média geral mistura climas muito diferentes.

---

## 3. Escolhas da análise (valem para os dois desafios)

Usar as mesmas escolhas no Python e no Excel deixa os dois relatórios coerentes: o Desafio 3 confirma, com outra ferramenta, o que o Desafio 2 descobriu.

| Item | Escolha recomendada | Por quê |
| --- | --- | --- |
| **Coluna numérica de análise** | `velocidade_vento_kmh` | Varia de verdade (0 a 157 km/h), tem média maior que a mediana, tem valores extremos reais (rajadas em tempestades) e muda bastante entre as condições do tempo. Dá material para todas as etapas: insight, outliers, assimetria e comparação de grupos |
| **Coluna de grupo da fato** | `condicao_tempo` | Só 4 valores (`ensolarado`, `nublado`, `chuvoso`, `tempestade`), fácil de comparar e de ler |
| **Coluna de grupo da dimensão** | `pais` | Só existe na dimensão, então obriga a juntar as tabelas (merge no Python, PROCX no Excel) |
| **Pergunta de negócio** (sugestão para você ajustar) | "Em quais condições do tempo e em quais países o vento chega a níveis que pedem alerta à população?" | É uma pergunta que um serviço meteorológico faria, e a resposta sai direto da coluna de vento |
| **Decisão que o resultado apoiaria** | Definir a partir de qual velocidade de vento emitir alerta, e em quais condições do tempo e países reforçar o monitoramento | É uma ação concreta, que alguém de fora da área entende |

> [!NOTE]
> As escolhas acima são uma recomendação. Se preferir outra coluna, evite estas duas:
> - **`pressao_hpa`**: a altitude domina o valor, então qualquer diferença entre grupos fica escondida.
> - **`precipitacao_mm`**: como quase tudo é zero, a mediana é 0 e a regra do IQR marca **toda leitura com chuva** como outlier.

---

## 4. Desafio 2 · Estatística com Python

**Ferramenta:** Google Colab, no notebook `Desafio2_Estatistica_Python` que já existe.
**Entrega:** o próprio notebook, com tabelas, gráficos e textos, no GitHub.
**O que é avaliado primeiro:** a **interpretação em texto**. Os gráficos somam pontos, mas não substituem a análise.

### 4.1 Ajustes no que já foi feito

| Onde | O que está hoje | O que fazer |
| --- | --- | --- |
| Célula de texto "Pergunta que eu quero responder" | Vazia e no meio do notebook (depois da célula 04) | **Mover para o topo** do notebook, logo abaixo do título, e preencher com os 3 itens do Passo 1 (pergunta, coluna e decisão) |
| Célula 09 | `COLUNA_NUMERICA = "nome_da_coluna_numerica"` | Trocar por `COLUNA_NUMERICA = "velocidade_vento_kmh"` |
| Célula 10 | Deu `NameError: name 'serie_numerica' is not defined` | O erro vem da célula 09. Depois de corrigir a 09, **execute a 09 e depois a 10** |
| "Leitura da conferência" | Já escrita, mas resumida | Acrescentar: o tamanho da dimensão (10.050 estações), que `data_hora` chegou como texto (`object`) e que isso é esperado pelo esquema, e que não há ausentes, então **nada precisa ser tratado** |
| "Analisando a média, mediana e desvio padrão" | O modelo ainda está com as lacunas entre parênteses | Preencher depois de rodar a célula 10 (ver 4.3) |
| Células de título | Algumas células de código não têm título | Colocar um título curto acima de cada célula (`Célula 01 · Importações`, `Célula 02 · Carregar os dados`...), para o instrutor achar cada etapa |

### 4.2 Passo 1 · Pergunta de negócio (célula de texto no topo)

Escreva com as suas palavras, neste formato:

```markdown
## Pergunta de negócio

- **Pergunta:** (a pergunta que um serviço meteorológico faria sobre o vento)
- **Coluna de análise:** `velocidade_vento_kmh`, da tabela fato `leituras`
- **Decisão que o resultado apoiaria:** (o que poderia mudar: limite de alerta, reforço de monitoramento...)
```

### 4.3 Passos 5 e 6 · Medidas e frases de insight (células 09 e 10 + texto)

1. Rode as células 09 e 10.
2. Anote na tabela de medidas: **média, mediana, desvio padrão, mínimo e máximo**.
3. Escreva as frases no modelo que já está no notebook. Para cada frase, siga as 3 partes:

| Parte | No vento |
| --- | --- |
| **Medida** | Cite o número copiado da célula 10 (por exemplo, "a média é X km/h") |
| **Leitura** | Compare com outra medida. A média está acima da mediana? Então poucas leituras com vento muito forte puxam a média para cima |
| **Decisão** | O que fazer: por exemplo, usar a mediana como "vento típico" e tratar as leituras muito altas como casos de alerta |

> [!TIP]
> **Para conferir se rodou certo:** a média do vento fica perto de **11,9 km/h**, a mediana perto de **9,7 km/h** e o máximo perto de **157 km/h**. Se os seus números estiverem muito diferentes, confira se a coluna escolhida é mesmo `velocidade_vento_kmh`.

### 4.4 Passo 7 · Comparar grupos (células 11, 12 e 13)

A célula 11 (junção) **já foi feita** e mostrou 62.000 linhas antes e depois. Esse é o resultado certo: a junção não duplicou registros. Falta criar as células 12 e 13.

| Célula | `COLUNA_GRUPO` | O que mostra |
| --- | --- | --- |
| 12 | `"condicao_tempo"` (da fato) | Média, mediana e desvio do vento em cada condição do tempo |
| 13 | `"pais"` (da dimensão, só existe em `df_completo` por causa da junção) | Média, mediana e desvio do vento em cada um dos 15 países |

O código das duas células é o do guia oficial (Parte 3, células 12 e 13); só muda o valor de `COLUNA_GRUPO`.

Depois de **cada** tabela, escreva uma célula de texto "Leitura por ...", respondendo:

- Qual grupo tem a **maior média** de vento? A **mediana** confirma?
- Qual grupo tem o **maior desvio padrão** (vento mais imprevisível)?
- Algum grupo tem **poucos registros** (`count` baixo) e pede cautela?
- Se as médias forem **quase iguais** entre os grupos, diga isso: significa que aquela categoria **não explica** a diferença de vento. Esse também é um resultado.

> [!NOTE]
> Na comparação por `condicao_tempo`, espere uma diferença clara (tempestade bem acima das outras). Na comparação por `pais`, observe se a diferença é grande ou pequena **antes** de tirar conclusões. As duas leituras são importantes para a recomendação.

### 4.5 Passo 8 · Outliers e valores ausentes (células 14 e 15 + texto)

Copie as células 14 e 15 do guia oficial (não têm lacunas) e rode.

O que observar:

- A célula 14 mostra o **limite superior** (perto de 31 km/h) e **quantas leituras passam dele** (mais de 2 mil).
- A célula 15 mostra se a **média muda muito** sem esses valores, e se a **mediana quase não muda**. Esse é o sinal clássico de outlier.

**Como decidir:** pergunte se o valor é **possível** para o tema. Vento forte em leitura de tempestade é um **caso real**, não um erro de digitação. Para justificar, você pode conferir em qual condição do tempo estão os maiores valores:

```python
# Em qual condição do tempo estão as leituras acima do limite superior?
df_completo[df_completo[COLUNA_NUMERICA] > limite_superior]["condicao_tempo"].value_counts()
```

Escreva a célula de texto "Leitura e decisão registrada":

- quantos valores a regra marcou, a partir de que valor, e como a média muda sem eles;
- **Decisão:** manter, tratar ou excluir, e por quê (o mais coerente com o tema é **manter**, porque são rajadas reais e são justamente os casos que um serviço meteorológico precisa enxergar);
- **Valores ausentes:** não há nenhum (célula 06), então nada foi ignorado, preenchido ou removido.

### 4.6 Passo 9 · Distribuição (células 16, 17 e 18 + texto)

Copie as três células do guia oficial (não têm lacunas). Elas calculam a **assimetria**, os **percentis** e as **faixas de valores**.

Na leitura, explique:

- **Assimetria:** o valor é positivo? Então a distribuição tem **cauda para a direita**: a maioria das leituras tem vento fraco ou moderado, e poucas têm vento muito forte.
- **Percentis:** até que velocidade ficam 75% e 90% das leituras.
- **Faixas:** quantas leituras estão na primeira faixa e quantas nas faixas mais altas.
- **Em palavras:** "a maioria das leituras fica entre X e Y km/h, e poucas passam de Z km/h".

### 4.7 Passo 10 · Recomendação (célula de texto)

É o centro do relatório. Precisa de **pelo menos duas medidas** com número.

```markdown
## Recomendação

**Pergunta de negócio:** (a mesma do topo do notebook)

**Evidência 1:** (por exemplo: a média e a mediana do vento, e o que a diferença entre elas mostra)
**Evidência 2:** (por exemplo: a média do vento em tempestade comparada às outras condições)

**Recomendação em uma frase:** (uma ação clara, como usar um limite de velocidade para emitir alerta e reforçar o monitoramento em leituras de tempestade)

**Limites da análise:** os dados são fictícios; os outliers foram mantidos porque são casos reais; não havia valores ausentes; as leituras não são periódicas (cada estação tem de 1 a 16 leituras).
```

Antes de seguir, confira: cada evidência tem **medida e número**, a recomendação é **uma ação** (não só uma descrição), e o texto não apresenta os dados fictícios como fato real.

### 4.8 Passo 11 · Gráficos (células 19 e 20 + texto) · diferencial

Só faça depois que os Passos 7 a 10 estiverem completos. Copie as células do guia oficial e preencha os eixos:

| Célula | Gráfico | `xlabel` | `ylabel` |
| --- | --- | --- | --- |
| 19 | Histograma do vento | `"Velocidade do vento (km/h)"` | `"Quantidade de leituras"` |
| 20 | Boxplot do vento | `"Velocidade do vento (km/h)"` | (não tem) |

Abaixo dos gráficos, escreva **uma frase** dizendo o que eles mostram (por exemplo, onde a maioria das leituras se concentra e onde ficam os pontos de vento forte). Gráfico sem frase de leitura não conta como análise.

### 4.9 Passo 12 · Registro de uso de IA e fechamento

- O guia pede que, **acima de cada trecho gerado ou ajustado com apoio de IA**, entre o registro com a data. Se nenhum trecho usou IA, não há o que registrar.
- Rode tudo do zero para garantir que não há erro: **Ambiente de execução → Reiniciar sessão e executar tudo**.
- Salve no GitHub: **Arquivo → Salvar uma cópia no GitHub** (ver a seção 6).

### 4.10 Ordem final das células do notebook

| # | Tipo | Conteúdo |
| --- | --- | --- |
| — | Texto | Título + **Pergunta de negócio** (Passo 1) |
| 01 | Código | Importações |
| 02 | Código | Carregar os CSV (`estacoes` e `leituras`) |
| 03 e 04 | Código | Conhecer a dimensão e a fato |
| 05 a 08 | Código | Tipos, ausentes, duplicadas, chave estrangeira |
| — | Texto | Leitura da conferência |
| 09 e 10 | Código | Coluna `velocidade_vento_kmh` e medidas |
| — | Texto | Frases de insight + decisão final |
| 11 | Código | Junção fato + dimensão |
| 12 | Código | Grupos por `condicao_tempo` |
| — | Texto | Leitura por condição do tempo |
| 13 | Código | Grupos por `pais` |
| — | Texto | Leitura por país |
| 14 e 15 | Código | IQR e medidas com/sem outliers |
| — | Texto | Leitura e decisão registrada |
| 16 a 18 | Código | Assimetria, percentis e faixas |
| — | Texto | Leitura da distribuição |
| — | Texto | **Recomendação** |
| 19 e 20 | Código | Histograma e boxplot |
| — | Texto | Frase de leitura dos gráficos |

---

## 5. Desafio 3 · Relatório dinâmico no Excel

**Ferramenta:** Excel para a Web, **sem Power Query**.
**Entrega:** a planilha `.xlsx` no GitHub, **no mesmo dia** da sessão.
**Arquivo final:** `Desafio3_Excel_Relatorio_Dinamico.xlsx`. O arquivo `Desafio3_Excel_Modelo Copiar.xlsx` é só o modelo de partida.

### 5.1 Escolhas para o Excel

| Item | Escolha | Onde é usada |
| --- | --- | --- |
| Coluna numérica | `velocidade_vento_kmh` | Tabela dinâmica, fórmulas por critério, indicadores |
| Coluna de grupo (linhas) | `condicao_tempo` | O modelo da aba `Relatorio` tem **exatamente 4 linhas de grupo** (linhas 3 a 6), que é o número de condições do tempo |
| Coluna do filtro | `pais`, trazido da dimensão com PROCX | Célula `Relatorio!B2`, com lista suspensa dos 15 países |
| Coluna de data | `data_hora` | Função `MÊS` (e `ANO`) |

> [!NOTE]
> No Excel, a **média** é a métrica principal do vento. A **soma** de velocidades não tem um significado físico, mas o `SOMASE` continua obrigatório: ele serve para **auditar** a tabela dinâmica (o total da fórmula precisa bater com o da tabela dinâmica) e é a base da média (soma ÷ quantidade).

### 5.2 Ajustes necessários: pontos em que o guia oficial e o modelo não batem

Leia isto **antes** de começar, para não travar no meio do caminho.

| # | Problema no guia | O que fazer |
| --- | --- | --- |
| 1 | Os números decimais do CSV usam **ponto** (`1.6`), e o Excel em português usa **vírgula** | Faça a troca **antes** de dividir as colunas (ver 5.3). Sem isso, os números viram texto ou data e todas as contas saem erradas |
| 2 | Na Ação 08, `=COLS(tbl_fatos)` tem um erro de digitação | Use `=COLS(tbl_fato)` |
| 3 | Na Ação 09, `Coluna_Numarica` tem um erro de digitação, e o nome da função de vazios varia | Use `=CONT.NÚM(tbl_fato[velocidade_vento_kmh])` e `=CONTAR.VAZIO(tbl_fato[velocidade_vento_kmh])` |
| 4 | As células da aba `Conferencia` no guia (B7 a B10 e B13) não batem com os rótulos do modelo (A2 a A13) | **Siga os rótulos do modelo** (ver 5.5) |
| 5 | A fórmula de chave duplicada `MÁXIMO(CONT.SE(...))` compara 62.000 linhas com 62.000 linhas, e pode **travar** o Excel para a Web | Use a versão leve com `ÚNICO` (ver 5.5) |
| 6 | Na Ação 14, a fórmula da linha 3 aponta para `$A6` e usa `multa_reais` | Na linha 3, use `$A3`, e troque a coluna por `velocidade_vento_kmh` |
| 7 | Na Ação 17, o primeiro argumento de `SOMASES` e `MÉDIASES` aparece como coluna de grupo | Em `SOMASES` e `MÉDIASES` o **primeiro argumento é a coluna numérica** (ver 5.9) |
| 8 | O guia pede os indicadores em `J6:J8`, mas o modelo tem os rótulos em `I5:I7`, ainda com textos do exemplo ("Total de multas", "Média por empréstimo") | Use `J5:J7` e **troque os rótulos** para o vento |
| 9 | O guia alterna entre PROCV e PROCX | Use **PROCX**: ele não exige que a chave seja a primeira coluna e já tem o "se não encontrar" embutido |
| 10 | Os cartões da aba `Dashboard` (Total, Média geral, Registros numéricos) só têm o rótulo, sem fórmula | Ligue cada cartão à aba `Relatorio` (ver 5.13) |
| 11 | A numeração das ações e dos passos muda entre o README e as partes do guia | Guie-se pelo **nome do passo**, não pelo número da ação |
| 12 | As leituras vão de janeiro de 2025 a julho de 2026, então os meses de 1 a 7 aparecem nos **dois anos** | Ao usar `MÊS`, crie também a coluna `ano` com `ANO`, para não misturar os anos |

### 5.3 Passo 2 · Trazer os dados para a planilha

1. **Crie a pasta de trabalho:** faça uma cópia do modelo e renomeie para **`Desafio3_Excel_Relatorio_Dinamico`**.
2. **Converta os CSV para UTF-8 com BOM** (para os acentos de `Japão`, `Canadá`, `África do Sul`... aparecerem certos). Coloque `estacoes.csv` e `leituras.csv` numa pasta `bases`, abra o Git Bash nela e rode o comando do guia (Ação 02). Os arquivos corrigidos ficam em `bases/corrigidos`.
3. **Abra o CSV no Excel para a Web** (Arquivo → Abrir → Abrir arquivo deste dispositivo) e clique em **Converter**.
4. **Troque o ponto decimal pela vírgula, antes de dividir as colunas.** Com todos os dados ainda na coluna A:
   - selecione a coluna A;
   - **Ctrl + H**: localizar `,` e substituir por `;` → **Substituir tudo**;
   - **Ctrl + H** de novo: localizar `.` e substituir por `,` → **Substituir tudo**.

   Essa troca é segura nesta base: nenhuma cidade, nome de estação ou data tem ponto ou vírgula no texto.
5. **Divida as colunas:** Dados → Dividir Texto em Colunas → delimitador **Ponto e vírgula**.
6. **Copie tudo** (Ctrl + A, Ctrl + C) e **cole em A1** da aba `dimensao` (para `estacoes`) ou da aba `fato` (para `leituras`).
7. **Repita** para o outro CSV.

**Confira a olho:**

- A linha 1 tem os nomes das colunas.
- `temperatura_c`, `velocidade_vento_kmh`, `latitude` etc. estão **alinhados à direita** (número de verdade) e com **vírgula** decimal.
- `data_hora` aparece como data e hora, alinhada à direita.
- Acentos certos em `pais` e `cidade`.

### 5.4 Passo 3 · Criar as tabelas

Em cada aba, clique nos dados, **Ctrl + T**, marque "A minha tabela tem cabeçalhos" e dê o nome em **Design da Tabela → Nome da Tabela**:

| Aba | Nome da tabela | Chave primária | Observação |
| --- | --- | --- | --- |
| `dimensao` | `tbl_dimensao` | `estacao_id` (já é a 1ª coluna) | 10.050 linhas |
| `fato` | `tbl_fato` | `leitura_id` | 62.000 linhas; `estacao_id` é a chave estrangeira |

Congele a linha superior nas duas abas (Exibir → Congelar Painéis → Congelar Linha Superior).

### 5.5 Passo 4 · Aba `Conferencia`

Preencha a coluna B seguindo os rótulos que **já estão** na coluna A do modelo:

| Célula | Rótulo do modelo | Fórmula / valor | Resultado esperado |
| --- | --- | --- | --- |
| B2 | Linhas da tabela dimensão | `=LINS(tbl_dimensao)` | 10050 |
| B3 | Colunas da tabela dimensão | `=COLS(tbl_dimensao)` | 7 |
| B4 | Linhas da tabela fato | `=LINS(tbl_fato)` | 62000 |
| B5 | Colunas da tabela fato | `=COLS(tbl_fato)` | 9 (cresce quando você criar colunas novas) |
| B6 | Coluna numérica escolhida | `velocidade_vento_kmh` (texto) | — |
| B7 | Valores numéricos | `=CONT.NÚM(tbl_fato[velocidade_vento_kmh])` | 62000 (igual a B4: todos são números) |
| B8 | Células vazias | `=CONTAR.VAZIO(tbl_fato[velocidade_vento_kmh])` | 0 |
| B9 | Chave primária repetida | `=SE(CONT.VALORES(ÚNICO(tbl_fato[leitura_id]))<LINS(tbl_fato);"Há duplicidade";"Todos são únicos")` | Todos são únicos |
| B10 | Chave estrangeira sem correspondência | `=SE(MENOR(CONT.SE(tbl_dimensao[estacao_id];tbl_fato[estacao_id]);1)=0;"Existe órfã";"Não existe órfã")` | Não existe órfã |
| B11 | Registros com "Sem cadastro" | preencher depois do Passo 6 | 0 |
| B12 | Nova coluna da tabela fato | `pais_dim` (texto) | — |
| B13 | Erros do PROCX | `=CONT.SE(tbl_fato[pais_dim];"Sem cadastro")` | 0 |

> [!WARNING]
> Se **B7 for menor que B4**, há números guardados como texto: volte ao item 4 da seção 5.3. Se a fórmula de B10 ficar lenta demais, ela pode ser deixada de lado: o B13 confere a mesma coisa depois do PROCX.

**Bloco "Leitura da conferência"** (aba `Leitura`, B6): tamanho das duas tabelas, que a coluna de vento tem 62.000 números e nenhum vazio, que a chave primária não se repete, que a chave estrangeira bate com a dimensão e que **não há valores ausentes em nenhuma coluna** (confira pelo filtro do cabeçalho: não aparece a opção "(Vazias)").

### 5.6 Passo 1 · Aba `Leitura`, cabeçalho e pergunta

- **B2 (Tema):** Serviço Meteorológico.
- **B3:** o seu nome.
- **B5:** a mesma pergunta de negócio do Desafio 2, com coluna numérica `velocidade_vento_kmh`, coluna de grupo `condicao_tempo` e a decisão que o resultado apoiaria.

### 5.7 Passo 5 · Tabela dinâmica (aba `Dinamica`)

1. Na aba `fato`, clique na tabela → **Inserir → Tabela Dinâmica → Da Tabela/Intervalo** → Planilha existente → `Dinamica!A3`.
2. Monte assim:

| Área | Campo | Resumir por | Nome personalizado |
| --- | --- | --- | --- |
| Linhas | `condicao_tempo` | — | — |
| Valores | `velocidade_vento_kmh` | **Média** | Vento médio (km/h) |
| Valores | `leitura_id` | **Contagem** | Quantidade de leituras |
| Valores | `velocidade_vento_kmh` | **Soma** | Soma do vento (para auditoria) |

3. Formate os números com 1 ou 2 casas decimais e ordene pela média, do maior para o menor.
4. Troque o título em `Dinamica!A1` para algo como "Vento por condição do tempo".

> [!TIP]
> A tabela dinâmica **não se atualiza sozinha**. Sempre que criar uma coluna nova na `fato`, clique com o botão direito na tabela dinâmica → **Atualizar**.

### 5.8 Passo 6 · PROCX: trazer o país para a fato

1. Na aba `fato`, na primeira célula vazia ao lado do último cabeçalho, digite **`pais_dim`** e Enter.
2. Na primeira linha de dados dessa coluna:

```excel
=PROCX([@estacao_id];tbl_dimensao[estacao_id];tbl_dimensao[pais];"Sem cadastro";0)
```

3. A fórmula preenche a coluna inteira. Confira `Conferencia!B13` (e B11): o resultado precisa ser **0**.
4. Atualize a tabela dinâmica: `pais_dim` passa a aparecer nos campos.

### 5.9 Passos 7 e 9 · Aba `Relatorio`: a tabela dinâmica feita com fórmulas

**Estrutura da aba:**

| Célula | Conteúdo |
| --- | --- |
| A1 | Título: "Vento por condição do tempo" |
| A2 / B2 | "Filtro (país)" / célula do filtro, com lista suspensa (Passo 11) |
| A3 a A6 | `ensolarado`, `nublado`, `chuvoso`, `tempestade` (escritos **exatamente** como na base, em minúsculas) |
| Linha 7 | Total |

**Cabeçalhos sugeridos (linha 2, de B a G, ou numa linha acima dos grupos):** Quantidade · Soma do vento · Vento médio · Quantidade no país · Soma no país · Vento médio no país.

**Fórmulas da linha 3** (depois, arraste até a linha 6):

```excel
B3:  =CONT.SE(tbl_fato[condicao_tempo];$A3)
C3:  =SOMASE(tbl_fato[condicao_tempo];$A3;tbl_fato[velocidade_vento_kmh])
D3:  =SEERRO(MÉDIASE(tbl_fato[condicao_tempo];$A3;tbl_fato[velocidade_vento_kmh]);"-")
E3:  =CONT.SES(tbl_fato[condicao_tempo];$A3;tbl_fato[pais_dim];$B$2)
F3:  =SOMASES(tbl_fato[velocidade_vento_kmh];tbl_fato[condicao_tempo];$A3;tbl_fato[pais_dim];$B$2)
G3:  =SEERRO(MÉDIASES(tbl_fato[velocidade_vento_kmh];tbl_fato[condicao_tempo];$A3;tbl_fato[pais_dim];$B$2);"-")
```

**Linha 7 (Total):** `=SOMA(B3:B6)` em B7, C7, E7 e F7. D7 e G7 ficam com "-".

> [!IMPORTANT]
> **Ordem dos argumentos:** em `SOMASE` e `MÉDIASE` o critério vem **antes** da coluna numérica; em `SOMASES` e `MÉDIASES` a coluna numérica vem **primeiro**.

**Conferências:**

- `B7` precisa ser **62000** (todas as leituras).
- `C7` precisa ser **igual à soma** da tabela dinâmica.
- Troque o país em `B2` e veja as colunas E, F e G mudarem: é isso que torna o relatório **dinâmico**.

**Indicadores gerais** (troque os rótulos do modelo):

| Rótulo (coluna I) | Fórmula (coluna J) |
| --- | --- |
| I5: Soma do vento (km/h) | `J5: =SOMA(tbl_fato[velocidade_vento_kmh])` |
| I6: Vento médio por leitura (km/h) | `J6: =MÉDIA(tbl_fato[velocidade_vento_kmh])` |
| I7: Leituras com número | `J7: =CONT.NÚM(tbl_fato[velocidade_vento_kmh])` |

`J6` deve dar o **mesmo valor da média do Desafio 2** (perto de 11,9 km/h). Se der, os dois relatórios conferem entre si.

### 5.10 Passo 8 · Formatação condicional e primeira leitura

**O que destacar:** as leituras de **vento forte**, usando o mesmo limite superior do IQR que você achou no Desafio 2 (célula 14, perto de 31 km/h). Assim o destaque tem uma justificativa estatística, e não é um número inventado.

1. Selecione a coluna `velocidade_vento_kmh` da aba `fato` (ou a coluna de média da aba `Relatorio`).
2. Página Inicial → Formatação Condicional → Realçar Regras das Células → **É Maior Que...** → o limite do IQR → uma cor só.
3. Para citar quantas leituras foram destacadas, use numa célula livre: `=CONT.SE(tbl_fato[velocidade_vento_kmh];">31,25")` (troque pelo seu limite).

**Bloco "Leitura da tabela dinâmica e das fórmulas"** (aba `Leitura`, B7):

- a condição com a maior média de vento e o valor;
- a quantidade de leituras dessa condição (é grande ou pequena? pede cautela?);
- quantas leituras foram destacadas como vento forte;
- uma decisão possível.

**Bloco "Leitura das médias"** (aba `Leitura`, B8):

- a média geral (`J6`) e o maior valor de vento;
- a média de duas condições comparadas;
- o que muda quando o filtro é um país específico (colunas E a G);
- uma decisão possível.

### 5.11 Passo 10 · Texto, vazios e datas (colunas novas na `tbl_fato`)

| Coluna nova | Fórmula | Para que serve |
| --- | --- | --- |
| `condicao_limpa` | `=ARRUMAR([@condicao_tempo])` | Remove espaços extras. Nesta base o resultado é **igual ao original** (não há espaços sobrando); registre isso |
| `faixa_vento` | `=SE(ÉCEL.VAZIA([@velocidade_vento_kmh]);"Sem leitura";SE([@velocidade_vento_kmh]>31,25;"Vento forte";"Vento normal"))` | Usa `SE` e `ÉCEL.VAZIA` juntos: confere se há vazio (não há) e classifica a leitura |
| `mes` | `=MÊS([@data_hora])` | Função de data obrigatória |
| `ano` | `=ANO([@data_hora])` | Separa 2025 de 2026 (os meses de janeiro a julho aparecem nos dois anos) |

> [!NOTE]
> Se `data_hora` ficou como **texto** (alinhada à esquerda), crie antes uma coluna com `=DATA.VALOR([@data_hora])` e formate como Data.

**Valores fora do padrão:** na seta do filtro de `velocidade_vento_kmh`, classifique do maior para o menor e veja os primeiros valores (o máximo fica perto de 157 km/h). Depois volte à ordem original classificando `leitura_id` do menor para o maior. A decisão coerente com o Desafio 2 é **manter**, porque são rajadas reais de tempestade.

**Bloco "Registro do tratamento"** (aba `Leitura`, B9): texto (ARRUMAR em `condicao_tempo`, sem mudanças), vazios (nenhum; a coluna `faixa_vento` confirma), datas (`MÊS` e `ANO` sobre `data_hora`) e valores fora do padrão (máximo, mínimo e a decisão de manter).

### 5.12 Passo 11 · Validação de dados e auditoria

**Lista suspensa no filtro (`Relatorio!B2`):** Dados → Validação de Dados → Permitir: **Lista** → Origem:

```text
Alemanha;Argentina;Austrália;Brasil;Canadá;Chile;Estados Unidos;França;Índia;Itália;Japão;México;Portugal;Reino Unido;África do Sul
```

(Se a lista não separar os itens, troque `;` por `,`.) Escreva uma mensagem de entrada ("Escolha um país") e um alerta de erro com estilo **Parar**. **Teste** digitando um país que não está na lista.

**Auditoria:** compare a **soma** da tabela dinâmica (aba `Dinamica`) com `Relatorio!C7`. Se forem diferentes, teste uma causa de cada vez: tabela dinâmica não atualizada, filtro ativo, número como texto ou nome de condição escrito diferente em A3:A6.

**Bloco "Leitura da auditoria"** (aba `Leitura`, B10): os dois números, se bateram e o que isso diz sobre a confiança no relatório.

### 5.13 Passo 12 · Gráficos e Dashboard

As tabelas dinâmicas e os gráficos do Dashboard devem ser **criados na própria aba `Dashboard`**.

| Elemento | Como montar | Título e eixos |
| --- | --- | --- |
| **Gráfico 1** | Tabela dinâmica com `condicao_tempo` em Linhas e média do vento em Valores → gráfico de **colunas** | "Vento médio por condição do tempo" · eixo X: Condição do tempo · eixo Y: Velocidade média do vento (km/h) |
| **Gráfico 2** | Mesma tabela dinâmica, com `pais_dim` na área de **Filtros** (ou uma segmentação de país) → gráfico de colunas que muda quando o país muda | "Vento médio por condição do tempo · país selecionado" · mesmos eixos |
| **Cartão Total** | `=Relatorio!J5` | Troque o rótulo para "Soma do vento" ou use a quantidade de leituras |
| **Cartão Média geral** | `=Relatorio!J6` | "Vento médio (km/h)" |
| **Cartão Registros** | `=Relatorio!J7` | "Leituras" |
| **Título** | `A1` | "Dashboard · Vento nas estações meteorológicas" |

Outra opção para a segunda tabela dinâmica do Dashboard: `mes` em Linhas e média do vento em Valores, para ver se o vento muda ao longo do ano.

**Bloco "Frase de leitura dos gráficos"** (aba `Leitura`, B11): uma frase dizendo o que os gráficos mostram e qual insight ilustram. Ela aparece sozinha no Dashboard (`B29 = Leitura!B11`).

### 5.14 Passo 13 · Recomendação (aba `Leitura`, B12)

Mesmo formato do Desafio 2, com números **copiados da planilha**:

- **Evidência 1:** por exemplo, a média de vento em tempestade (`Relatorio!D6`) comparada à média geral (`J6`).
- **Evidência 2:** por exemplo, quantas leituras passaram do limite de vento forte (formatação condicional) e em qual condição elas se concentram.
- **Recomendação em uma frase.**
- **Limites da análise:** dados fictícios; nenhum "Sem cadastro" (B13 = 0); nenhum vazio; valores extremos mantidos por serem reais.

A recomendação aparece sozinha no Dashboard (`B33 = Leitura!B12`).

### 5.15 Diferencial (só depois de tudo pronto)

- **FILTRO:** numa área livre, liste as leituras de vento forte de um país:
  `=FILTRO(tbl_fato;(tbl_fato[pais_dim]=Relatorio!B2)*(tbl_fato[faixa_vento]="Vento forte");"Nenhum registro")`
  Deixe espaço livre abaixo e à direita, senão aparece `#DESPEJAR!`.
- **Imagem:** o ícone do tema no cabeçalho do Dashboard.
- Na aba `Leitura`, B13: duas linhas explicando como esses recursos enriquecem o relatório.

### 5.16 Registro de uso de IA e revisão final

- O guia pede uma tabela na aba `Leitura` (linhas 16 e 17) para cada trecho feito com apoio de IA. Se nenhum trecho usou IA, escreva isso na linha 17.
- Atualize a tabela dinâmica e procure erros com **Ctrl + F** por `#` (`#N/D`, `#VALOR!`, `#NOME?`, `#DIV/0!`). Corrija todos.
- Baixe a cópia: **Arquivo → Salvar Como → Baixar uma Cópia**.

---

## 6. Entrega no GitHub

O repositório do projeto é o **`desafio-meteorologia`**. Guarde os arquivos na pasta `exercicios`, junto do Desafio 1:

| Desafio | Arquivo no repositório | Como enviar |
| --- | --- | --- |
| 2 | `exercicios/Desafio2_Estatistica_Python.ipynb` | No Colab: **Arquivo → Salvar uma cópia no GitHub**, escolhendo o repositório e o caminho acima |
| 3 | `exercicios/Desafio3_Excel_Relatorio_Dinamico.xlsx` | No GitHub, abra a pasta `exercicios` → **Add file → Upload files** → arraste o `.xlsx` → **Commit changes** (limite de 25 MB) |

Depois, entregue no **Canvas** o link do repositório.

> [!TIP]
> O arquivo `Desafio3_Excel_Modelo Copiar.xlsx` é só o modelo. Quando a versão final estiver no repositório, ele pode ser removido para não confundir quem for avaliar.

---

## 7. Lista final de conferência

### Desafio 2

| Item | Feito |
| --- | --- |
| Pergunta de negócio no topo do notebook | ☐ |
| Célula 09 com `velocidade_vento_kmh` e célula 10 rodando sem erro | ☐ |
| Frases de insight com média, mediana e desvio padrão, **citando os números** | ☐ |
| Células 12 e 13 (grupos por `condicao_tempo` e por `pais`), cada uma com leitura | ☐ |
| Células 14 e 15 (outliers) com a decisão registrada (manter) e a nota sobre ausentes | ☐ |
| Células 16 a 18 (distribuição) com leitura | ☐ |
| Recomendação com pelo menos **duas medidas** | ☐ |
| Histograma e boxplot com título, eixos e frase de leitura (diferencial) | ☐ |
| Registro de uso de IA, conforme a regra do guia | ☐ |
| "Reiniciar sessão e executar tudo" sem erro | ☐ |
| Notebook salvo no GitHub | ☐ |

### Desafio 3

| Item | Feito |
| --- | --- |
| Arquivo renomeado para `Desafio3_Excel_Relatorio_Dinamico` | ☐ |
| CSV convertidos para UTF-8 com BOM e decimais com vírgula | ☐ |
| `tbl_dimensao` (10.050 linhas) e `tbl_fato` (62.000 linhas) criadas | ☐ |
| Aba `Conferencia` preenchida (B2 a B13), com B13 = 0 | ☐ |
| Tabela dinâmica: `condicao_tempo` × média, contagem e soma do vento | ☐ |
| Coluna `pais_dim` com PROCX | ☐ |
| `Relatorio`: CONT.SE, SOMASE, MÉDIASE, CONT.SES, SOMASES, MÉDIASES, SEERRO, SOMA, MÉDIA, CONT.NÚM | ☐ |
| Total da fórmula igual ao da tabela dinâmica | ☐ |
| Formatação condicional destacando vento forte | ☐ |
| Colunas com ARRUMAR, SE + ÉCEL.VAZIA, MÊS e ANO | ☐ |
| Lista suspensa de países em `Relatorio!B2`, testada | ☐ |
| Dois gráficos com título e eixos no `Dashboard`, e cartões ligados à `Relatorio` | ☐ |
| Aba `Leitura` com todos os blocos (B5 a B12) preenchidos com números | ☐ |
| Registro de uso de IA, conforme a regra do guia | ☐ |
| Nenhuma célula com erro (`#`) | ☐ |
| `.xlsx` enviado ao GitHub e link entregue no Canvas | ☐ |
