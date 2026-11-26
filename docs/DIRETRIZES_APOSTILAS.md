# Diretrizes para elaboração das apostilas — Serviço Meteorológico

**Projeto:** [desafio-meteorologia](https://github.com/casthielcastro/desafio-meteorologia)  
**Público-alvo:** estudantes do curso de Analista de Dados da Generation  
**Finalidade:** documento de referência para criação, revisão e manutenção das três apostilas  
**Versão:** 1.1 — 26/11/2026

## 1. Objetivo dos documentos

Produzir três apostilas técnico-pedagógicas de aprendizagem autodidata orientada por projetos, capazes de conduzir o estudante desde a interpretação dos requisitos até a implementação, validação e entrega de cada desafio.

O objetivo não é ensinar o aluno a copiar uma solução pronta, mas ensiná-lo a construir a solução, compreendendo o que precisa fazer, por onde começar, o que observar, quais perguntas formular, como tomar decisões, por que executar cada operação e como verificar o resultado.

O projeto de Serviço Meteorológico deve ser o fio condutor de toda a aprendizagem. Python, Pandas, estatística, APIs e Excel são ferramentas para resolver os problemas do projeto, não disciplinas a serem ensinadas isoladamente.

## 2. Perfil do estudante

O perfil de referência é um estudante que:

- Está há algumas semanas no curso de Analista de Dados da Generation.
- Possui conhecimentos muito básicos de Python e pouca experiência prática na resolução autônoma de problemas.
- Conseguiu concluir o Desafio 1, mas com dificuldades.
- Iniciou o Desafio 2, encontrou dificuldades e interrompeu seu desenvolvimento.
- Pode ter lacunas nos conceitos já apresentados durante o curso.

Esse perfil orienta o nível de detalhamento, mas não significa que todos os leitores tenham as mesmas dificuldades. Não presumir domínio de Pandas, estatística, APIs, modelagem de dados ou Excel apenas porque o aluno já teve contato com essas ferramentas. O nível de conhecimento prévio em Excel não foi estabelecido.

O texto deve ser acessível a um adulto iniciante, sem infantilização e sem transformar a apostila em um curso introdutório genérico de programação.

## 3. Organização da coleção

Criar três documentos independentes, complementares e com metodologia e identidade editorial consistentes:

| Documento | Foco | Resultado de aprendizagem |
|---|---|---|
| Guia do Desafio 1 | Construção, geração e integração dos dados | Transformar requisitos em uma base estruturada, consistente e validada. |
| Guia do Desafio 2 | Análise estatística e tomada de decisão | Investigar dados, selecionar técnicas e elaborar conclusões fundamentadas. |
| Guia do Desafio 3 | Relatório analítico no Excel | Organizar, analisar e comunicar resultados em um relatório funcional e confiável. |

Cada estudante deve conseguir abrir diretamente o guia do desafio que está realizando, sem precisar ler integralmente os anteriores. Reintroduzir brevemente o contexto, a estrutura dos dados, os requisitos específicos e os conceitos prévios indispensáveis. Evitar repetir explicações extensas que não sejam necessárias ao desafio atual.

A independência dos documentos é didática, não uma alteração nas dependências ou exigências oficiais do projeto. O Desafio 1 utiliza a base gerada pelo estudante; os Desafios 2 e 3 utilizam a base oficial do repositório. Explicitar essa distinção nos guias pertinentes. Os Desafios 2 e 3 devem produzir resultados compatíveis quando utilizarem os mesmos dados, critérios e métodos de cálculo.

## 4. Fundamentação pedagógica

Aplicar os princípios abaixo de forma integrada e contextualizada. As referências são fundamentos para a elaboração editorial, não conteúdo teórico obrigatório para o aluno.

| Princípio | Aplicação prática |
|---|---|
| Aprendizagem contextualizada e significativa | Relacionar conceitos novos ao problema meteorológico e ao conhecimento prévio do estudante. |
| Aprendizagem orientada por projetos | Utilizar a construção do desafio como atividade central de aprendizagem. |
| Instrução explícita | Demonstrar procedimentos, decisões, conceitos e resultados esperados, sem omitir etapas relevantes. |
| Exemplos resolvidos (worked examples) | Apresentar soluções explicadas, com exemplos pequenos antes da aplicação completa quando isso facilitar a compreensão. |
| Suporte gradual (scaffolding) | Dividir tarefas complexas em partes compreensíveis e aumentar a complexidade progressivamente. |
| Metacognição e modelagem do raciocínio | Tornar explícitas as perguntas, estratégias de planejamento, decisões e verificações que um profissional realizaria. |
| Teoria da carga cognitiva | Introduzir informações no momento em que forem necessárias, evitando excesso de conceitos simultâneos e redundâncias. |
| Desenho Universal para a Aprendizagem (DUA) | Organizar informações de maneira acessível, com hierarquia clara, vocabulário explicado e representações complementares quando úteis. |
| Aprendizagem multimídia | Usar recursos visuais intencionalmente, respeitando coerência, sinalização, contiguidade, segmentação e complementaridade. |

O suporte gradual deve ocorrer dentro do próprio desenvolvimento do projeto, sem criar exercícios extras, testes ou atividades avaliativas.

## 5. Sequência didática de cada tópico

A sequência a seguir deve orientar a escrita. Não é necessário transformar todos os itens em subtítulos fixos: a narrativa deve ser natural, evitando repetição mecânica.

1. **Contextualização:** apresentar o problema concreto e sua relação com o desafio.
2. **Requisitos:** identificar o que precisa ser atendido e quais restrições existem.
3. **Questionamentos:** demonstrar o que o estudante deveria perguntar antes de executar.
4. **Construção do raciocínio:** explicar como as respostas levam à solução, incluindo dependências e alternativas relevantes.
5. **Fundamentação:** introduzir os conceitos técnicos necessários àquela etapa.
6. **Planejamento:** organizar o procedimento e sua ordem de execução.
7. **Implementação:** demonstrar o passo a passo, incluindo os códigos, fórmulas ou operações necessários.
8. **Explicação técnica:** explicar o funcionamento, as entradas, as saídas e as decisões relevantes.
9. **Resultado esperado:** mostrar o que o aluno deve observar após executar.
10. **Validação e interpretação:** ensinar a conferir o resultado, identificar erros, corrigi-los e interpretar seu significado quando aplicável.
11. **Continuidade:** relacionar o que foi construído à próxima etapa.

Quando pertinente, indicar o objetivo de aprendizagem no início do capítulo. Os questionamentos devem vir acompanhados de desenvolvimento e explicação; não devem funcionar como perguntas de prova nem exigir que o aluno descubra sozinho uma solução que ainda não conhece.

### 5.1. Construção do raciocínio

Explicitar o processo de decisão antes de apresentar a implementação. Ensinar o aluno a decompor problemas, identificar dependências, escolher informações relevantes e reconhecer condições que precisam ser satisfeitas.

Sempre que útil, demonstrar como o problema poderia ser resolvido manualmente com poucos registros e, em seguida, mostrar como a ferramenta automatiza a mesma lógica. A simulação manual é uma estratégia de explicação, não uma atividade adicional a ser entregue.

**Exemplo:** antes de mostrar `groupby()` no Desafio 2, explicar por que uma média geral não permite comparar condições meteorológicas, separar uma pequena amostra por condição e calcular as médias de cada grupo. Só então apresentar a implementação em Pandas e interpretar o resultado.

### 5.2. Progressão de complexidade

- Começar por uma representação simples quando o problema for abstrato ou complexo.
- Demonstrar operações intermediárias e seus resultados antes de reunir várias operações em uma expressão mais compacta, quando necessário.
- Passar de pequenas amostras à base completa após a compreensão da lógica.
- Não introduzir simultaneamente vários conceitos novos de negócio, estatística e programação sem preparar o estudante.
- Ajustar o detalhamento à dificuldade real da etapa; não expandir operações simples desnecessariamente.

## 6. Diretrizes para conteúdo técnico

### 6.1. Ensino contextualizado das ferramentas

Não criar capítulos isolados para ensinar Python, Pandas, Excel, matemática ou estatística de forma genérica. Introduzir conceitos no momento em que forem necessários para resolver um problema do projeto.

Quando uma função, método, biblioteca, estrutura de programação, fórmula ou técnica estatística for relevante, explicar:

- O problema que motivou sua utilização.
- O que faz e por que foi escolhida.
- Como funciona naquele contexto, incluindo parâmetros ou sintaxe relevantes.
- O que recebe e o que produz.
- Como interpretar e validar seu resultado.

Explicações linha por linha são adequadas para trechos complexos ou desconhecidos do público, mas não são obrigatórias para toda instrução. Evitar tanto blocos extensos sem explicação quanto explicações de sintaxe que desviem o foco do desafio.

### 6.2. Implementação e rastreabilidade

- Usar os nomes de tabelas, colunas, variáveis e arquivos compatíveis com o projeto; explicar simplificações didáticas explicitamente.
- Fornecer código e fórmulas completos nas etapas em que o estudante precisa executá-los, incluindo pré-requisitos, importações e dependências necessárias.
- Manter a sequência de execução coerente com o notebook ou a planilha.
- Diferenciar requisitos oficiais de escolhas de implementação e sugestões complementares.
- Explicar alternativas quando ajudarem a compreender uma decisão, sem apresentar todas as possibilidades existentes.
- Não expor credenciais, chaves ou outros segredos. Explicar a configuração segura quando pertinente.
- Mostrar resultados intermediários e finais verificáveis, distinguindo valores demonstrativos dos resultados obtidos na base oficial.
- Não inventar resultados, funcionalidades ou requisitos que não estejam sustentados pelas fontes do projeto.
- Não tratar concordância entre Python e Excel como prova absoluta de correção: conferir também dados, filtros, fórmulas e critérios de cálculo.
- Explicitar que os dados meteorológicos são fictícios e que conclusões sobre alertas são restritas ao contexto didático, não recomendações operacionais para o clima real.

### 6.3. Validação e resolução de problemas

Incluir pontos de verificação ao longo da implementação, não apenas no final. Ensinar o estudante a identificar o resultado correto, reconhecer sintomas de erro, investigar causas prováveis e corrigir o problema antes de avançar.

Quando pertinente, demonstrar erros comuns e suas consequências. No final de cada apostila, incluir um checklist de entrega alinhado aos requisitos oficiais do desafio. O checklist é uma ferramenta de conferência prática, não uma avaliação de conhecimentos.

## 7. Linguagem e estilo editorial

- Português brasileiro, linguagem clara, profissional, acolhedora e apropriada para adultos.
- Explicar termos técnicos na primeira ocorrência ou no ponto em que se tornam necessários.
- Preferir exemplos do serviço meteorológico a analogias de outros domínios.
- Contextualizar antes de apresentar uma solução; evitar expressões como “basta fazer” quando o procedimento exige conhecimentos não explicados.
- Manter parágrafos organizados, títulos informativos e instruções em sequência lógica.
- Evitar jargões sem explicação, redundâncias, excesso de teoria e saltos de raciocínio.
- Utilizar glossários contextualizados; uma consolidação ao final pode ser incluída se facilitar a consulta, sem duplicação desnecessária.
- Não estabelecer um número fixo de páginas: priorizar compreensão, completude e organização.

### 7.1. Recursos editoriais recorrentes

Usar, quando contribuírem para a leitura, blocos como:

| Recurso | Finalidade |
|---|---|
| Pense antes de executar | Apresentar perguntas orientadoras e desenvolver as respostas. |
| Conceito fundamental | Explicar um conceito necessário à etapa. |
| Construindo o raciocínio | Demonstrar como chegar à solução. |
| Na prática / Implementação | Apresentar código, fórmulas ou procedimentos. |
| Decisão técnica | Justificar uma escolha e indicar alternativas relevantes. |
| Atenção / Erros comuns | Explicar riscos, limitações, erros e correções. |
| Ponto de verificação | Conferir o resultado antes de prosseguir. |

Esses blocos são opcionais e devem ser usados com moderação. Não exigir todos em cada tópico.

## 8. Recursos visuais

Adicionar imagens, diagramas, gráficos, tabelas e capturas de tela **somente quando contribuírem efetivamente para a compreensão**. Não estabelecer quantidade mínima de imagens por capítulo ou documento.

### 8.1. Quando utilizar

| Recurso | Aplicação pedagógica |
|---|---|
| Diagramas relacionais | Visualizar tabelas, chaves e relacionamentos. |
| Fluxogramas | Compreender ordem, dependências, decisões e validações. |
| Tabelas demonstrativas | Representar pequenas amostras ou resultados intermediários. |
| Diagramas de transformação | Mostrar os dados antes e depois de agrupamentos, junções ou filtros. |
| Gráficos | Explicar distribuições, padrões, comparações e valores extremos. |
| Capturas de tela | Orientar procedimentos de interface no Colab, Google Cloud, Sheets ou Excel quando necessário. |
| Ilustrações conceituais | Tornar conceitos abstratos mais compreensíveis quando o texto isolado for insuficiente. |

### 8.2. Regras de qualidade visual

- **Coerência:** excluir informações e imagens decorativas sem função didática.
- **Sinalização:** destacar os elementos que o estudante deve observar.
- **Contiguidade:** posicionar figuras e explicações próximas.
- **Segmentação:** dividir representações complexas quando isso facilitar a leitura.
- **Complementaridade:** integrar texto e imagem sem repetição desnecessária.
- Garantir legibilidade, contraste, tamanho adequado e precisão técnica; não depender exclusivamente de cores para transmitir significado.
- Incluir títulos, legendas, unidades e indicações de leitura quando necessários.
- Identificar exemplos fictícios, representações simplificadas e gráficos baseados em dados oficiais.
- Evitar capturas de tela inventadas ou desatualizadas apresentadas como se fossem reais.

Regra de decisão: se o recurso visual não torna o assunto mais claro, o texto é suficiente.

## 9. Estrutura proposta — Guia do Desafio 1

**Foco:** transformar requisitos em uma base de dados estruturada, consistente e validada, utilizando Python e integração com Google Sheets.

**Competências:** interpretação de requisitos, decomposição de problemas, modelagem relacional, dependências entre dados, geração de registros, integração e validação.

### Capítulos sugeridos

1. Apresentação do desafio.
2. Contextualização do serviço meteorológico.
3. Entendendo os requisitos e as entregas.
4. Conhecendo a estrutura dos dados e os relacionamentos entre tabelas.
5. Identificando as regras de negócio e as dependências entre variáveis.
6. Planejando a solução e a ordem de geração dos dados.
7. Preparando o ambiente de desenvolvimento.
8. Configurando a integração com o Google Sheets e a autenticação segura.
9. Construindo e testando a geração de estações com uma base reduzida.
10. Construindo a geração de leituras meteorológicas.
11. Gerando a base completa.
12. Validando os dados e as regras de negócio.
13. Exportando os arquivos CSV.
14. Enviando os dados para o Google Sheets.
15. Conferindo os resultados e preparando a entrega.

### Cuidados específicos

Revisar os fundamentos utilizados no desafio dentro de sua aplicação prática: funções, listas, dicionários, sorteios ponderados, DataFrames, chaves, CSV e API. Explicar a relação entre estação e leitura antes de implementar a geração, incluindo por que a estação deve existir primeiro. Demonstrar como testar a solução em pequena escala antes de gerar a base completa.

A apostila deve permitir tanto a realização inicial do desafio quanto a consolidação do conhecimento de quem já o concluiu com dificuldades.

## 10. Estrutura proposta — Guia do Desafio 2

**Foco:** investigar a base oficial e construir uma recomendação de negócio fundamentada em análise estatística com Python e Pandas.

**Competências:** exploração e qualidade de dados, seleção de variáveis, interpretação estatística, comparação de grupos, pensamento crítico e tomada de decisão.

### Capítulos sugeridos

1. Apresentação do desafio.
2. Contextualização do problema meteorológico.
3. Entendendo os requisitos e as entregas.
4. Entendendo a pergunta de negócio.
5. Conhecendo e importando a base oficial.
6. Explorando a estrutura dos dados.
7. Verificando a qualidade dos dados.
8. Identificando as variáveis relevantes.
9. Calculando e interpretando medidas estatísticas.
10. Compreendendo a distribuição dos dados.
11. Comparando condições meteorológicas.
12. Relacionando as tabelas.
13. Comparando resultados entre países.
14. Identificando, analisando e decidindo o tratamento de outliers.
15. Construindo e interpretando gráficos.
16. Transformando resultados em insights.
17. Elaborando uma recomendação de negócio.
18. Reconhecendo as limitações da análise.
19. Conferindo e preparando a entrega.

### Cuidados específicos

Dedicar atenção especial a este guia, pois foi a etapa em que o estudante de referência encontrou dificuldades suficientes para interromper o projeto.

Trabalhar de forma integrada três camadas: o significado da medida estatística, sua implementação em Python/Pandas e sua interpretação para a decisão de negócio. Quando necessário, usar pequenas amostras para explicar média, mediana, desvio padrão, quartis, amplitude interquartil, agrupamentos e outliers, antes de aplicar os cálculos à base oficial.

Ensinar não só como calcular uma medida, mas por que ela é adequada, o que indica, quais são suas limitações e como a conclusão se conecta à pergunta de negócio. Não remover valores extremos automaticamente apenas por serem outliers.

## 11. Estrutura proposta — Guia do Desafio 3

**Foco:** construir um relatório analítico funcional e compreensível no Excel a partir da base oficial.

**Competências:** organização de informações, fórmulas, tabelas dinâmicas, visualização, validação cruzada e comunicação de resultados.

### Capítulos sugeridos

1. Apresentação do desafio.
2. Contextualização do problema meteorológico.
3. Entendendo os requisitos e as entregas.
4. Planejando a estrutura do relatório e considerando quem o utilizará.
5. Importando a base oficial.
6. Organizando e validando os dados.
7. Relacionando as informações das tabelas.
8. Calculando indicadores com fórmulas.
9. Construindo tabelas dinâmicas.
10. Comparando grupos.
11. Analisando valores extremos.
12. Escolhendo os gráficos adequados.
13. Construindo o relatório visual.
14. Apresentando os insights.
15. Validando os resultados e conferindo sua compatibilidade com o Desafio 2.
16. Conferindo e preparando a entrega.

### Cuidados específicos

Demonstrar a transferência do raciocínio analítico para outra ferramenta. Quando útil, relacionar agrupamentos do Pandas a tabelas dinâmicas, funções de média e mediana às fórmulas do Excel, e junções às técnicas adequadas de relacionamento no Excel. Tratar essas correspondências como aproximações didáticas, não equivalências universais.

Não presumir que o leitor já concluiu ou compreendeu integralmente o Desafio 2. Retomar os conceitos estatísticos indispensáveis dentro de sua aplicação. Orientar a construção de um relatório utilizável por outra pessoa, evitando elementos visuais que não apoiem a interpretação.

## 12. Conteúdos que não devem ser incluídos

- Seções “Teste seus conhecimentos”.
- Questionários, provas ou avaliações de aprendizagem.
- Exercícios complementares desvinculados do desenvolvimento do desafio.
- Gabaritos.
- Aulas isoladas e genéricas de Python, Pandas, matemática, estatística ou Excel.
- Código pronto sem contextualização ou justificativa.
- Imagens meramente decorativas.
- Repetições extensas que não tenham finalidade pedagógica.
- Requisitos inventados ou apresentados como obrigatórios sem respaldo nas instruções oficiais.

As perguntas de raciocínio, demonstrações manuais, exemplos resolvidos, validações práticas e checklists de entrega continuam permitidos e são parte da metodologia. A própria construção do projeto é a atividade de aprendizagem.

## 13. Fontes de referência e processo de elaboração

Utilizar como fontes técnicas do projeto:

- Repositório [desafio-meteorologia](https://github.com/casthielcastro/desafio-meteorologia).
- Instruções originais dos três desafios e documentação do tema Serviço Meteorológico.
- Base oficial, notebooks e planilha de implementação disponíveis no projeto.
- Documentos existentes: **Guia do projeto · Serviço Meteorológico** e **Guia do código · Serviço Meteorológico**, aproveitando explicações, exemplos e diagramas úteis sem simplesmente juntar os dois documentos.
- Documentações oficiais das tecnologias empregadas, quando necessário.

Antes de redigir cada apostila, cruzar requisitos, documentação e implementação. Identificar divergências e lacunas, sem corrigi-las silenciosamente nem converter escolhas da implementação em exigências oficiais. Organizar o conteúdo na ordem em que o aluno precisaria pensar e executar, que pode diferir da ordem atual dos arquivos.

Os sumários deste documento são propostas editoriais, não uma transcrição verificada dos requisitos oficiais. Ajustá-los após a conferência das fontes de cada desafio. Não acrescentar funcionalidades, indicadores ou entregas obrigatórias sem sustentação documental.

## 14. Critérios de qualidade e revisão final

Utilizar o checklist abaixo para revisar cada apostila antes de publicá-la.

### Independência e cobertura

- [ ] O aluno consegue abrir diretamente a apostila do desafio atual.
- [ ] O contexto, os dados utilizados, os requisitos e as entregas estão claros.
- [ ] Todos os requisitos oficiais foram contemplados e diferenciados das sugestões de implementação.
- [ ] As etapas seguem uma sequência lógica e executável.

### Qualidade pedagógica

- [ ] O problema e o raciocínio são apresentados antes do código ou da fórmula.
- [ ] As perguntas orientadoras são desenvolvidas e respondidas na narrativa.
- [ ] Os conceitos aparecem no momento em que são necessários, sem aulas genéricas.
- [ ] A complexidade e o detalhamento são adequados ao estudante iniciante.
- [ ] Os exemplos ajudam a compreender a solução sem desviar do projeto.
- [ ] As decisões técnicas possuem justificativa suficiente.
- [ ] Não há exercícios extras, avaliações, questionários ou gabaritos.

### Qualidade técnica

- [ ] Os nomes, dados e procedimentos são compatíveis com as fontes verificadas.
- [ ] Os códigos e fórmulas necessários estão completos, coerentes e foram testados quando possível.
- [ ] Os resultados esperados e intermediários estão demonstrados e identificados corretamente.
- [ ] Existem orientações para validar, interpretar e corrigir problemas.
- [ ] As limitações, simplificações e hipóteses estão explícitas.
- [ ] Não há exposição de credenciais ou segredos.
- [ ] O checklist de entrega corresponde aos requisitos oficiais.

### Qualidade editorial e visual

- [ ] A linguagem é clara, profissional, acessível e consistente.
- [ ] Os títulos, blocos, tabelas e demais elementos facilitam a navegação.
- [ ] Cada recurso visual possui finalidade pedagógica demonstrável.
- [ ] Diagramas, gráficos e imagens são legíveis e tecnicamente corretos.
- [ ] Não há redundâncias ou conteúdo decorativo desnecessário.

## 15. Referências pedagógicas de apoio

- David Ausubel — aprendizagem significativa.
- Lev Vygotsky — aprendizagem mediada e zona de desenvolvimento proximal.
- John Sweller — teoria da carga cognitiva e exemplos resolvidos.
- Barak Rosenshine — princípios de instrução.
- Richard E. Mayer — teoria cognitiva da aprendizagem multimídia.
- [Australian Education Research Organisation (AERO)](https://www.edresearch.edu.au/) — instrução explícita e suporte à aprendizagem.
- [Education Endowment Foundation (EEF)](https://educationendowmentfoundation.org.uk/) — metacognição e aprendizagem autorregulada.
- [CAST — Universal Design for Learning Guidelines](https://udlguidelines.cast.org/) — Desenho Universal para a Aprendizagem.

Estas referências registram a fundamentação pedagógica discutida na definição das apostilas. Sua aplicação ao ensino técnico de adultos é uma adaptação editorial ao contexto deste projeto; não implica que a metodologia seja uma diretriz oficial da Generation. As referências externas não foram submetidas a nova revisão bibliográfica nesta consolidação.

## 16. Critério central de sucesso

Ao acompanhar o guia, o estudante deve conseguir compreender o que precisa fazer, por onde começar, o que considerar, como implementar, por que a solução funciona e como verificar se está correta. Deve também desenvolver um raciocínio que possa transferir para outros projetos de análise de dados.

**O objetivo não é ensinar o aluno a copiar o projeto. É ensinar o aluno a construir o projeto.**

## 17. Revisão de aderência à implementação (26/11/2026)

As apostilas foram publicadas em [Guia 1](../guia_aluno/Guia_Desafio_1.md), [Guia 2](../guia_aluno/Guia_Desafio_2.md) e [Guia 3](../guia_aluno/Guia_Desafio_3.md), como Markdown independente. Os PDFs e DOCX anteriores foram preservados como referências; não são versões atualizadas destas apostilas.

| Ponto | Constatação | Tratamento na documentação |
|---|---|---|
| D1: volume | Notebook gera 10.050 estações e 62.000 leituras; roteiro exige pelo menos 20.000 somadas | Distinguir mínimo de escolha de implementação |
| D1: umidade | Gerador limita a 20–100%, mas assert original aceita 0–100% | Recomendar validação alinhada ao esquema |
| D1: conferência do Sheets | Cabeçalhos e quantidades são lidos da planilha, mas FK é verificada nos DataFrames locais | Acrescentar verificação dos IDs efetivamente gravados |
| D2: origem e qualidade | Notebook usa base oficial, verifica linhas duplicadas e FK; não testa diretamente unicidade de ambas as PK | Acrescentar verificação de PK e junção many_to_one |
| D2: recomendação | Saídas salvas contam 2.300 leituras acima de 31,25 km/h; texto arredonda para acima de 31 km/h | Manter critério estrito >31,25 para reproduzir as contagens |
| D2: interpretação | Comparações descritivas não comprovam causalidade, ausência de efeito geográfico nem risco real | Restringir conclusões à simulação |
| D3: busca | Roteiro alterna PROCV e PROCX; guia de código usa PROCX com busca binária | Explicar ambos; exigir ordenação crescente para busca binária e confirmar requisito com instrutor |
| D3: Dashboard | Introdução o chama de diferencial, parte final exige gráficos nessa aba | Incluir Dashboard e explicitar inconsistência |
| D3: auditoria | Guias anteriores registram média 11,94 e soma 740.166,8; concordância entre métodos não comprova importação correta | Orientar conferência de tipos, filtros, chaves e indicadores |

Escopo da revisão: leitura dos roteiros, esquema meteorológico, código dos notebooks e saídas salvas do Desafio 2, além dos guias anteriores. Não houve execução do Colab ou acesso autenticado à planilha Google. A leitura binária do Excel não retornou conteúdo; o Desafio 3 permanece pendente de inspeção direta e teste de fórmulas, filtros e gráficos. Não declarar validação integral até realizar esses testes. Nenhum notebook ou planilha foi modificado. Os complementos técnicos das apostilas são recomendações identificadas, não alterações já realizadas no projeto.
