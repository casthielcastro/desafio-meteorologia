# Guia do Desafio 1 — Serviço Meteorológico
**Construção, geração e integração dos dados | Curso de Analista de Dados**

Este guia acompanha você desde a leitura dos requisitos até a entrega. Você vai construir uma base fictícia, não baixar uma base pronta. Não é necessário dominar Python: os conceitos serão explicados no momento em que forem usados.

## 1. O que precisa ser entregue

**Objetivo:** saber o que precisa estar pronto no final, antes de escrever qualquer código.

Na sessão síncrona, conecte o Colab ao Google Sheets, escreva o cabeçalho e dez estações, e leia os dados de volta. Na etapa assíncrona, gere as duas tabelas completas, valide, exporte os CSV, carregue a planilha e confira a entrega. O roteiro original exige pelo menos 20.000 registros somados. A implementação deste tema escolhe 10.050 estações e 62.000 leituras, totalizando 72.050. Entregue o notebook, os dois CSV e a cópia da planilha com os dados no GitHub; envie o link no Canvas.

Com a entrega clara, o próximo passo é entender o que esses dados representam.

## 2. Entenda o negócio antes de programar

**Objetivo:** entender por que os dados ficam em duas tabelas e como elas se ligam.

Uma rede de estações meteorológicas registra condições do tempo, temperatura, umidade, chuva, vento e pressão. Mais adiante, queremos investigar a partir de qual velocidade um vento se torna incomum na base e em quais condições isso acontece. Neste desafio, porém, ainda não precisamos calcular o limite: precisamos construir dados que permitam essa análise.

Pergunte-se: o que descreve uma estação? O que descreve um evento observado por ela? Uma mesma estação pode ter mais de uma leitura? O país muda a cada leitura? Ao separar cadastro de eventos, evitamos repetir os dados do cadastro e mantemos uma referência confiável.

| estacoes (exemplo ilustrativo) | cidade | pais |
|---|---|---|
| 1 | Faro | Portugal |
| 2 | Tóquio | Japão |

| leituras (exemplo ilustrativo) | estacao_id | vento (km/h) |
|---|---:|---:|
| 1 | 1 | 8 |
| 2 | 1 | 30 |
| 3 | 2 | 12 |

As leituras 1 e 2 pertencem à mesma estação. Se a leitura 3 apontasse para a estação 5, não poderíamos localizar seu cadastro. O identificador único de uma tabela é a chave primária (PK); a referência à chave de outra tabela é a chave estrangeira (FK). Essa ligação é chamada integridade referencial.





![Relacionamento entre estacoes e leituras por estacao_id](assets/compartilhadas/relacionamento_tabelas.svg)

*Esquema parcial das colunas: cada leitura pertence a uma estação existente.*

O esquema completo é obrigatório:

| Tabela | Coluna | Tipo e finalidade |
|---|---|---|
| estacoes | estacao_id | inteiro, PK |
| estacoes | nome_estacao | texto, código EST-00001 |
| estacoes | cidade | texto, coerente com país |
| estacoes | pais | texto, 15 países |
| estacoes | latitude, longitude | decimais, caixa geográfica aproximada |
| estacoes | altitude_m | inteiro de 0 a 3499 |
| leituras | leitura_id | inteiro, PK, ordem cronológica |
| leituras | estacao_id | inteiro, FK |
| leituras | data_hora | data e hora |
| leituras | condicao_tempo | texto, quatro condições |
| leituras | temperatura_c | decimal, °C |
| leituras | umidade_pct | decimal, 20 a 100% |
| leituras | precipitacao_mm | decimal, milímetros |
| leituras | velocidade_vento_kmh | decimal, km/h |
| leituras | pressao_hpa | decimal, hPa |

Agora que sabemos quais colunas cada tabela precisa ter, vamos ver as regras que definem os valores dessas colunas.

## 3. Transforme as regras em um plano

**Objetivo:** descobrir o que depende do quê, para decidir a ordem em que os dados serão gerados.

A documentação não pede valores independentes. O país determina as opções de cidade e coordenadas; a condição meteorológica determina os parâmetros de vento, umidade, chuva, temperatura e pressão. A altitude e a latitude também influenciam parte das medidas. Portanto, a ordem de geração importa.

A dependência mais importante é entre as tabelas. Cada leitura guarda um estacao_id, e esse número só pode ser o ID de uma estação que já existe. Por isso as estações são geradas primeiro. Se gerássemos as leituras antes, elas apontariam para estações que ainda não foram criadas, e a ligação entre as tabelas ficaria quebrada. Além disso, a leitura usa a latitude e a altitude da estação para calcular a temperatura e a pressão, então precisa encontrar esse cadastro pronto.

| Países | Peso de cada país |
|---|---:|
| Brasil | 30% |
| Estados Unidos | 10% |
| Argentina, Canadá, Alemanha, França | 6% |
| Portugal, Japão, Austrália, México | 5% |
| África do Sul, Índia | 4% |
| Reino Unido, Itália | 3% |
| Chile | 2% |

| Condição | Probabilidade | Umidade média | Vento médio | Chuva |
|---|---:|---:|---:|---|
| nublado | 50% | 65% | 12 km/h | zero |
| ensolarado | 38% | 45% | 10 km/h | zero |
| chuvoso | 9% | 85% | 14 km/h | positiva |
| tempestade | 3% | 92% | 28 km/h | pelo menos 5 mm |

Esses percentuais são probabilidades de sorteio, não contagens exatas. Uma caixa geográfica é um retângulo aproximado: não garante que a coordenada fique na cidade ou mesmo em terra firme. A simulação abrange janeiro de 2025 a julho de 2026; as leituras não são periódicas. Não apresente os dados como observações reais.

![Fluxo do desafio 1](assets/desafio_01/fluxo_desafio.svg)

*Sequência das etapas do desafio.*

Com o plano pronto, falta preparar o lugar onde o código vai rodar e a planilha que vai receber os dados.

## 4. Prepare o ambiente e a conexão

**Objetivo:** conectar o notebook (no Colab ou no seu computador) à planilha do Google sem expor a credencial.

Crie um repositório público para os desafios conforme o roteiro do curso (projeto_desafios_tecnicos_ad). Abra o [Google Cloud Console](https://console.cloud.google.com/), crie um projeto e ative Google Sheets API e Google Drive API em APIs e serviços > Biblioteca. Em IAM e administração > Contas de serviço, crie uma conta de serviço, sem conceder papéis amplos desnecessários. Abra Chaves > Adicionar chave > Criar nova chave > JSON. Copie o client_email do JSON. Se a organização bloquear chaves, procure o instrutor.

Abra [sheets.new](https://sheets.new/), nomeie a planilha Desafio_01_Meteorologia, compartilhe como Editor com o client_email da conta de serviço e copie o ID que fica entre /d/ e /edit no endereço da planilha. Abra um notebook novo no [Colab](https://colab.research.google.com/). No painel Secrets (ícone de chave), crie GCP_SA_JSON com o conteúdo completo do JSON e GS_ID com o ID da planilha; habilite o acesso do notebook às duas secrets. Não publique a chave, nem em código, nem em saídas. Trabalhe numa planilha dedicada: o código de envio posterior limpa as abas existentes.

**Onde você vai rodar o notebook?** O código deste guia funciona nos dois lugares. A única diferença é onde ficam guardados a credencial e o ID da planilha:

- **No Colab:** use as Secrets `GCP_SA_JSON` e `GS_ID`, como descrito acima.
- **No computador (VS Code):** salve o JSON da conta de serviço com o nome `credenciais.json` e crie um arquivo `gs_id.txt` só com o ID da planilha, sem espaços nem aspas. Os dois ficam na mesma pasta do notebook. Escolha como kernel um Python que tenha as bibliotecas instaladas (veja a Célula 01). Antes de usar o Git, crie um arquivo `.gitignore` com os nomes `credenciais.json` e `gs_id.txt`, para a chave não ir para o repositório.

O código descobre sozinho onde está rodando, então você usa o mesmo notebook nos dois casos.

Uma API permite que o código solicite operações ao serviço externo. A conta de serviço identifica quem faz o pedido, mas só consegue editar a planilha quando ela é compartilhada. O gspread encapsula essas chamadas. Não confunda o Colab com Apps Script.

### Célula 01 — Instalar bibliotecas

```python
%pip install -q gspread faker
```

O comando instala gspread, que manipula planilhas, e Faker, que gera exemplos fictícios. O Colab já disponibiliza pandas. Usei `%pip` no lugar de `!pip` porque ele instala no mesmo Python que está rodando o notebook, e isso vale tanto no Colab quanto no VS Code. Com `!pip` no computador, a biblioteca pode ser instalada em outro Python e dar `ModuleNotFoundError` na célula seguinte. Se isso acontecer, reinicie o kernel. Execute as células de cima para baixo; se reiniciar o ambiente, as variáveis desaparecem.

### Célula 02 — Autenticar

No notebook, esta é uma célula só; aqui ela aparece em partes para facilitar a leitura.

**Parte 1 — Importar as bibliotecas.** Traz as ferramentas usadas no notebook: o gspread para a planilha, o Faker para inventar nomes, o pandas para as tabelas e as funções de data.

```python
# Bibliotecas usadas ao longo do notebook.
import json, gspread, random, os, time, math
from faker import Faker
import pandas as pd

# date, datetime e timedelta são usados para criar e somar datas.
from datetime import date, datetime, timedelta
```

**Parte 2 — Descobrir se está no Colab.** Tenta importar as ferramentas do Colab. No Colab isso funciona e `EM_COLAB` fica `True`. No computador esse módulo não existe, o erro é capturado e `EM_COLAB` fica `False`.

```python
# No Colab, as credenciais ficam nas Secrets. Fora do Colab (ex.: VS Code),
# o código usa os arquivos credenciais.json (credencial) e gs_id.txt (ID da planilha).
try:
    from google.colab import files, userdata
    EM_COLAB = True
except ModuleNotFoundError:
    EM_COLAB = False
```

**Parte 3 — Função que lê a credencial e o ID.** Cria a função `ler_segredo`, que busca o valor no lugar certo: na Secret, se estiver no Colab, ou no arquivo, se estiver no computador.

```python
def ler_segredo(nome):
    # Devolve o valor de um segredo, buscando no lugar certo para cada ambiente.
    if EM_COLAB:
        return userdata.get(nome)
    arquivo = "credenciais.json" if nome == "GCP_SA_JSON" else "gs_id.txt"
    with open(arquivo, encoding="utf-8") as f:
        return f.read().strip()
```

**Parte 4 — Entrar com a credencial.** Lê a credencial, cria a conexão com o Google e mostra o e-mail da conta para confirmar que deu certo.

```python
# Lê o conteúdo do JSON da credencial (Secret GCP_SA_JSON) e converte o texto em dicionário.
info = json.loads(ler_segredo("GCP_SA_JSON"))

# Cria o cliente do gspread autenticado com a service account.
gc = gspread.service_account_from_dict(info)

# Exibe o e-mail da credencial para confirmar que a autenticação funcionou.
print("Conectado como:", info["client_email"])
```

Se você estiver no Colab, `ler_segredo` usa o `userdata.get`. No computador, ela abre o arquivo. Nos dois casos, `json.loads` converte o texto em uma estrutura de dados e `service_account_from_dict` cria o cliente autenticado. A mensagem com o e-mail identifica a conta; ainda não prova que ela tem acesso à planilha. Não imprima o JSON.

> **Ponto de verificação:** aparece "Conectado como:" com o e-mail da conta de serviço. Se aparecer `FileNotFoundError` no computador, confira se `credenciais.json` e `gs_id.txt` estão na mesma pasta do notebook. Se aparecer `ModuleNotFoundError: No module named 'gspread'`, o kernel está usando um Python sem a biblioteca: troque o kernel ou rode a Célula 01 de novo e reinicie.

### Célula 03 — Abrir a planilha

No notebook, esta é uma célula só; aqui ela aparece em partes para facilitar a leitura.

**Parte 1 — O ID da planilha e o nome da aba.** Busca o ID da planilha (na Secret `GS_ID` ou no arquivo `gs_id.txt`) e define o nome que a primeira aba vai receber.

```python
# Lê o ID da planilha guardado na Secret GS_ID.
SHEET_ID = ler_segredo("GS_ID")

# Nome da tabela dimensão do tema: será o nome da primeira aba da planilha.
NOME_TABELA = "estacoes"
```

**Parte 2 — Abrir a planilha e renomear a aba.** Abre a planilha, pega a primeira aba, troca o nome dela para estacoes e mostra o título e o link para você conferir.

```python
# Abre a planilha pelo ID e seleciona a primeira aba (ws será usada nas próximas células).
sh = gc.open_by_key(SHEET_ID)
ws = sh.sheet1

# Renomeia a aba com o nome da tabela e exibe os dados da planilha.
ws.update_title(NOME_TABELA)
print(sh.title, "|", ws.title, "|", sh.url)
```

O ID identifica o arquivo, enquanto ws aponta para uma aba.

> **Ponto de verificação:** o título mostrado é o do seu arquivo e a aba foi renomeada para estacoes. Se aparecer erro 403 ou SpreadsheetNotFound, confira as APIs, o ID e o compartilhamento com a conta de serviço antes de seguir.

A conexão funciona. Agora podemos testar a escrita na planilha com poucos dados.

## 5. Teste o esquema e gere dez estações

**Objetivo:** montar a função que cria uma estação e testá-la com só dez registros antes de gerar a base inteira.

### Célula 04 — Escrever cabeçalho

```python
# Nomes EXATOS das colunas da tabela dimensão, na mesma ordem da documentação do tema.
COLUNAS_DIMENSAO = ["estacao_id", "nome_estacao", "cidade", "pais",
                    "latitude", "longitude", "altitude_m"]

# Escreve o cabeçalho a partir da célula A1.
ws.update(range_name="A1", values=[COLUNAS_DIMENSAO])

# Lê a linha 1 de volta e compara com COLUNAS_DIMENSAO.
print("Cabeçalho confere:", ws.row_values(1) == COLUNAS_DIMENSAO)
```

A lista determina a ordem dos valores. values espera uma lista de linhas; por isso envolvemos o cabeçalho em outra lista. Esperamos True. Utilize uma aba vazia no primeiro teste.

### Célula 05 — Construir o cadastro

Antes de sortear uma estação, precisamos definir seus dados dependentes. Se Portugal for selecionado, a cidade deve vir das opções portuguesas e as coordenadas da caixa de Portugal. Para cada registro, queremos um ID diferente. A função recebe i e devolve um dicionário, isto é, os nomes das colunas associados aos respectivos valores.

Uma função de sorteio comum daria as mesmas chances a todos os países. random.choices recebe pesos: [30,10,...] distribui as probabilidades conforme a documentação. Como retorna uma lista, [0] seleciona o resultado. CAIXA_PAIS[pais] busca os quatro limites da caixa; random.uniform sorteia entre os limites. O Faker inventa cidades brasileiras. A semente fixa ajuda a repetir os sorteios no mesmo ambiente.

Três detalhes de Python aparecem nessa célula:

- `f"EST-{i:05d}"` é uma f-string. O `f` antes das aspas deixa colocar uma variável entre chaves dentro do texto. O `:05d` completa o número com zeros até ter 5 dígitos: com i = 7, o resultado é EST-00007.
- `fake.city() if pais == "Brasil" else random.choice(CIDADES_PAIS[pais])` é um if/else numa linha só. Se o país for Brasil, o Faker inventa a cidade; senão, sorteamos uma das cinco cidades da lista do país.
- `[gerar_linha(i) for i in range(1, 11)]` é um laço for escrito de forma curta. Para cada i de 1 a 10, chama a função e guarda o resultado numa lista. O range(1, 11) para antes do 11, por isso gera exatamente dez números.

No notebook, esta é uma célula só; aqui ela aparece em partes para facilitar a leitura.

**Parte 1 — Preparar os sorteios.** Cria o gerador de nomes do Faker e fixa as sementes, para os sorteios saírem iguais toda vez que a célula rodar.

```python
# Gerador do Faker em português do Brasil (usado nas cidades brasileiras e nas datas).
fake = Faker("pt_BR")

# Sementes fixas: a cada execução os valores aleatórios saem iguais (resultado reproduzível).
Faker.seed(42)
random.seed(42)
```

**Parte 2 — Os países e a chance de cada um.** Lista os 15 países e o peso de cada um no sorteio, como pede a documentação. O Brasil aparece em cerca de 30% das estações.

```python
# Países e pesos definidos na documentação do tema (a soma dá 100).
PAISES = ["Brasil", "Estados Unidos", "Argentina", "Canadá", "Alemanha", "França",
          "Portugal", "Japão", "Austrália", "México", "África do Sul", "Índia",
          "Reino Unido", "Itália", "Chile"]
PESOS_PAISES = [30, 10, 6, 6, 6, 6, 5, 5, 5, 5, 4, 4, 3, 3, 2]
```

**Parte 3 — A área de cada país.** Para cada país, a faixa de latitude e de longitude em que a estação pode ser colocada.

```python
# Caixa geográfica aproximada de cada país: (latitude mínima, latitude máxima, longitude mínima, longitude máxima).
CAIXA_PAIS = {
    "Brasil":         (-33.0,   5.0, -73.0, -35.0),
    "Estados Unidos": ( 25.0,  49.0, -124.0, -67.0),
    "Argentina":      (-55.0, -22.0, -73.0, -54.0),
    "Canadá":         ( 42.0,  70.0, -140.0, -53.0),
    "Alemanha":       ( 47.3,  55.0,   6.0,  15.0),
    "França":         ( 42.5,  51.0,  -4.5,   8.0),
    "Portugal":       ( 37.0,  42.0,  -9.5,  -6.2),
    "Japão":          ( 31.0,  45.0, 129.0, 145.0),
    "Austrália":      (-39.0, -11.0, 113.0, 153.0),
    "México":         ( 15.0,  32.0, -117.0, -87.0),
    "África do Sul":  (-34.8, -22.0,  16.5,  32.9),
    "Índia":          (  8.0,  35.0,  68.0,  97.0),
    "Reino Unido":    ( 50.0,  58.6,  -8.0,   1.8),
    "Itália":         ( 37.0,  47.0,   6.6,  18.5),
    "Chile":          (-55.0, -17.5, -75.6, -67.0),
}
```

**Parte 4 — As cidades de cada país.** Cinco cidades por país. Para o Brasil não há lista: o Faker inventa o nome da cidade.

```python
# Lista de 5 cidades por país (o Brasil usa nomes gerados pelo Faker).
CIDADES_PAIS = {
    "Estados Unidos": ["Nova York", "Chicago", "Denver", "Seattle", "Miami"],
    "Argentina":      ["Buenos Aires", "Córdoba", "Rosário", "Mendoza", "Salta"],
    "Canadá":         ["Toronto", "Montreal", "Vancouver", "Calgary", "Ottawa"],
    "Alemanha":       ["Berlim", "Munique", "Hamburgo", "Colônia", "Frankfurt"],
    "França":         ["Paris", "Lyon", "Marselha", "Toulouse", "Bordéus"],
    "Portugal":       ["Lisboa", "Porto", "Coimbra", "Braga", "Faro"],
    "Japão":          ["Tóquio", "Osaka", "Sapporo", "Fukuoka", "Nagoya"],
    "Austrália":      ["Sydney", "Melbourne", "Brisbane", "Perth", "Adelaide"],
    "México":         ["Cidade do México", "Guadalajara", "Monterrey", "Puebla", "Mérida"],
    "África do Sul":  ["Joanesburgo", "Cidade do Cabo", "Durban", "Pretória", "Porto Elizabeth"],
    "Índia":          ["Nova Délhi", "Mumbai", "Bangalore", "Chennai", "Calcutá"],
    "Reino Unido":    ["Londres", "Manchester", "Edimburgo", "Birmingham", "Glasgow"],
    "Itália":         ["Roma", "Milão", "Nápoles", "Turim", "Florença"],
    "Chile":          ["Santiago", "Valparaíso", "Concepción", "Antofagasta", "Punta Arenas"],
}
```

**Parte 5 — A função que monta uma estação.** Sorteia o país primeiro e, a partir dele, escolhe a cidade, as coordenadas e a altitude. Devolve a estação como um dicionário, com o nome de cada coluna.

```python
def gerar_linha(i):
    # Devolve UM registro fictício de estação, no formato {nome_da_coluna: valor}.
    # "i" é o número da linha e serve de identificador sequencial.

    # Coluna de origem: o país é sorteado primeiro e define cidade e coordenadas.
    pais = random.choices(PAISES, weights=PESOS_PAISES)[0]
    lat_min, lat_max, lon_min, lon_max = CAIXA_PAIS[pais]
    cidade = fake.city() if pais == "Brasil" else random.choice(CIDADES_PAIS[pais])

    return {
        "estacao_id": i,                                           # identificador sequencial (PK)
        "nome_estacao": f"EST-{i:05d}",                            # código no formato EST-00001
        "cidade": cidade,                                          # cidade coerente com o país
        "pais": pais,                                              # país sorteado com os pesos da documentação
        "latitude": round(random.uniform(lat_min, lat_max), 4),    # dentro da caixa do país
        "longitude": round(random.uniform(lon_min, lon_max), 4),   # dentro da caixa do país
        "altitude_m": random.randint(0, 3499),                     # altitude em metros (0 a 3.499)
    }
```

**Parte 6 — Testar com 10 estações e enviar.** Gera 10 estações, mostra a primeira para conferir e envia as 10 para a planilha, na mesma ordem do cabeçalho.

```python
# Gera 10 registros de teste.
linhas = [gerar_linha(i) for i in range(1, 11)]
print(linhas[0])

# Converte cada registro em lista de valores NA ORDEM do cabeçalho e envia para a planilha.
valores = [[linha[c] for c in COLUNAS_DIMENSAO] for linha in linhas]
ws.append_rows(valores, value_input_option="RAW")
print(len(valores), "linhas enviadas")
```

O laço embutido em linhas chama a função dez vezes. O próximo passo transforma cada dicionário em uma lista na mesma ordem do cabeçalho; append_rows acrescenta as linhas. RAW evita reinterpretar os valores.

**Resultado esperado:** o print mostra um dicionário parecido com `{'estacao_id': 1, 'nome_estacao': 'EST-00001', 'cidade': ..., 'pais': ..., 'latitude': ..., 'longitude': ..., 'altitude_m': ...}`, seguido de "10 linhas enviadas". Cidade, país e números dependem do sorteio.

> **Atenção:** se repetir essa célula, haverá registros adicionais. Limpe a aba de teste e reescreva o cabeçalho antes de repetir.

### Célula 06 — Conferir o que chegou

```python
# Lê de volta tudo o que está na aba para conferir o que realmente chegou à planilha.
valores_lidos = ws.get_all_values()

print("Linhas (cabeçalho + dados):", len(valores_lidos))    # esperado: 11
print("Cabeçalho confere:", valores_lidos[0] == COLUNAS_DIMENSAO)
print(valores_lidos[:3])
```

> **Ponto de verificação:** espere 11 linhas (um cabeçalho e dez estações), cabeçalho igual e registros coerentes. Abra a planilha para conferir. Se houver 21 linhas, provavelmente o envio de teste ocorreu duas vezes. Corrija agora, não durante a análise.

O cadastro de estações funciona em pequena escala. Agora vamos montar a outra tabela, a das leituras, que depende dele.

## 6. Construa a tabela de leituras

**Objetivo:** criar a função que gera uma leitura ligada a uma estação existente, respeitando as regras do tempo.

### Célula 07 — Definir o esquema

No notebook, esta é uma célula só; aqui ela aparece em partes para facilitar a leitura.

**Parte 1 — O nome e as colunas da tabela de leituras.** Define o nome da segunda aba e a lista exata de colunas das leituras, na ordem da documentação.

```python
# Nome da tabela fato do tema: será o nome da segunda aba da planilha.
NOME_TABELA_FATO = "leituras"

# Nomes EXATOS das colunas da tabela fato, na mesma ordem da documentação do tema.
COLUNAS_FATO = ["leitura_id", "estacao_id", "data_hora", "condicao_tempo",
                "temperatura_c", "umidade_pct", "precipitacao_mm",
                "velocidade_vento_kmh", "pressao_hpa"]
```

**Parte 2 — A chave estrangeira.** Diz qual coluna liga a leitura à estação e confere se ela está mesmo na lista de colunas.

```python
# Coluna da tabela fato que é a chave estrangeira (guarda o ID da tabela dimensão).
COLUNA_FK = "estacao_id"

# Confere se a chave estrangeira é mesmo uma das colunas da tabela fato.
assert COLUNA_FK in COLUNAS_FATO, "COLUNA_FK precisa ser um dos nomes de COLUNAS_FATO"
print(NOME_TABELA_FATO, "|", COLUNAS_FATO)
```

O assert interrompe o programa quando sua condição não é verdadeira. Agora o esquema declara qual campo conecta a leitura ao cadastro.

### Célula 08 — Gerar uma leitura

Imagine três estações com IDs 1, 2 e 3. Se sorteássemos quatro leituras livremente, poderíamos obter 1, 1, 1, 3, deixando a estação 2 sem leitura. Por isso as primeiras leituras percorrem a lista de IDs, e só as demais são sorteadas. Precisamos também consultar latitude e altitude do cadastro, sem sorteá-las novamente.

A condição do tempo é escolhida antes das medidas. AJUSTE_CONDICAO guarda, na ordem, ajuste de temperatura, média e desvio de umidade, escala do vento e ajuste de pressão. A distribuição gama produz muitos ventos baixos e poucos altos. A média teórica é a forma vezes a escala: na tempestade, forma 2 e escala 14 dão 28 km/h; no tempo ensolarado, a escala é 5 e a média fica em 10 km/h. A distribuição normal varia em torno de uma média; a exponencial produz muitas chuvas pequenas e poucas grandes. Não é necessário derivar essas distribuições: elas traduzem as regras de simulação fornecidas. A função limita a umidade entre 20 e 100, calcula a temperatura com sazonalidade invertida por hemisfério e efeito diário, e reduz a pressão à medida que a altitude aumenta.

No notebook, esta é uma célula só; aqui ela aparece em partes para facilitar a leitura.

**Parte 1 — As condições do tempo e o efeito de cada uma.** Define as 4 condições com a chance de cada uma e, numa tabela, quanto cada condição muda a temperatura, a umidade, o vento e a pressão.

```python
# Condições do tempo e pesos definidos na documentação do tema.
CONDICOES = ["nublado", "ensolarado", "chuvoso", "tempestade"]
PESOS_CONDICOES = [50, 38, 9, 3]

# Efeito de cada condição sobre as demais variáveis (tabela acima).
AJUSTE_CONDICAO = {
    #             temperatura, umidade média, umidade desvio, escala do vento, pressão
    "ensolarado": (+1.5, 45, 12,  5, +1),
    "nublado":    ( 0.0, 65, 12,  6,  0),
    "chuvoso":    (-2.0, 85,  7,  7, -3),
    "tempestade": (-4.0, 92,  5, 14, -8),
}
```

**Parte 2 — O período das leituras.** As leituras acontecem entre 01/01/2025 e 31/07/2026.

```python
# Período simulado das leituras: 01/01/2025 a 31/07/2026.
INICIO_LEITURAS = datetime(2025, 1, 1)
FIM_LEITURAS = datetime(2026, 7, 31, 23, 59, 59)
```

**Parte 3 — Começo da função: estação, data e condição.** Escolhe a estação sem inventar ID, busca a latitude e a altitude dela, sorteia o momento da leitura e a condição do tempo.

```python
def gerar_linha_fato(i, ids_dim, info_dim):
    # Devolve UMA leitura fictícia, no formato {nome_da_coluna: valor}.
    # "i" é o número da linha; "ids_dim" é a lista de IDs que existem na tabela dimensão;
    # "info_dim" guarda (latitude, altitude_m) de cada estação.

    # --- Chave estrangeira: primeiro uma leitura para cada estação, depois sorteio ---
    estacao_id = ids_dim[i - 1] if i <= len(ids_dim) else random.choice(ids_dim)
    latitude, altitude = info_dim[estacao_id]

    # --- Valores de origem: o momento da leitura e a condição do tempo ---
    momento = fake.date_time_between(start_date=INICIO_LEITURAS, end_date=FIM_LEITURAS)
    condicao = random.choices(CONDICOES, weights=PESOS_CONDICOES)[0]
    aj_temp, umid_media, umid_desvio, escala_vento, aj_pressao = AJUSTE_CONDICAO[condicao]
```

**Parte 4 — Ainda na função: a temperatura.** Calcula a temperatura juntando a latitude, a altitude, a época do ano (invertida no hemisfério sul), a hora do dia e a condição.

```python
    # --- Temperatura: latitude, altitude, época do ano, hora do dia e condição ---
    dia_do_ano = momento.timetuple().tm_yday
    sazonal = 6 * math.cos(2 * math.pi * (dia_do_ano - 196) / 365.25)   # pico em meados de julho no hemisfério norte
    if latitude < 0:
        sazonal = -sazonal                                              # estações invertidas no hemisfério sul
    hora = momento.hour + momento.minute / 60
    diaria = 5 * math.cos(2 * math.pi * (hora - 15) / 24)               # máxima por volta das 15 h
    temperatura = (25 - abs(latitude) / 3 - 0.0065 * altitude
                   + sazonal + diaria + aj_temp + random.gauss(0, 2))
```

**Parte 5 — Ainda na função: umidade, vento, pressão e chuva.** Cada medida depende da condição do tempo. A chuva só existe em leituras chuvosas ou de tempestade; nas outras, é zero.

```python
    # --- Umidade, vento e pressão dependem da condição ---
    umidade = min(100.0, max(20.0, random.gauss(umid_media, umid_desvio)))
    vento = random.gammavariate(2, escala_vento)
    pressao = random.gauss(1013, 8) - 0.10 * altitude + aj_pressao

    # --- Chuva: só existe em leituras chuvoso e tempestade ---
    if condicao == "chuvoso":
        chuva = max(0.1, round(random.expovariate(1 / 4), 1))
    elif condicao == "tempestade":
        chuva = round(5 + random.expovariate(1 / 7), 1)
    else:
        chuva = 0.0
```

**Parte 6 — Fim da função: devolver a leitura.** Junta tudo numa leitura, com os nomes das colunas do esquema e os valores arredondados.

```python
    return {
        "leitura_id": i,                                        # identificador (renumerado em ordem cronológica na Célula 09)
        "estacao_id": estacao_id,                               # CHAVE ESTRANGEIRA: um ID que existe na dimensão
        "data_hora": momento.strftime("%Y-%m-%d %H:%M:%S"),     # data e hora da leitura
        "condicao_tempo": condicao,                             # ensolarado, nublado, chuvoso ou tempestade
        "temperatura_c": round(temperatura, 1),                 # graus Celsius
        "umidade_pct": round(umidade, 1),                       # % entre 20 e 100
        "precipitacao_mm": chuva,                               # milímetros (0 quando não chove)
        "velocidade_vento_kmh": round(vento, 1),                # km/h
        "pressao_hpa": round(pressao, 1),                       # hPa
    }
```

**Parte 7 — Testar a função.** Gera uma leitura usando as 10 estações de teste, para conferir antes de gerar a base inteira.

```python
# Teste com as 10 estações de teste da Célula 05.
info_teste = {l["estacao_id"]: (l["latitude"], l["altitude_m"]) for l in linhas}
print(gerar_linha_fato(1, list(info_teste), info_teste))
```

Observe a ordem: estação → localização e data → condição → medidas. O return reúne o registro completo.

**Resultado esperado:** um dicionário com as nove colunas das leituras, como `{'leitura_id': 1, 'estacao_id': 1, 'data_hora': '2025-...', 'condicao_tempo': ..., ...}`. Os valores dependem do sorteio.

> **Ponto de verificação:** o estacao_id está entre 1 e 10 (as estações de teste), o vento não é negativo e a chuva é zero em tempo seco ou positiva em chuvoso/tempestade.

As duas funções funcionam isoladamente. O próximo passo é chamá-las milhares de vezes.

## 7. Gere a base completa

**Objetivo:** gerar as 10.050 estações e as 62.000 leituras e organizá-las como tabelas.

Agora que o cadastro e o evento funcionam isoladamente, podemos escalá-los. Precisamos organizar milhares de dicionários como tabelas. O DataFrame do pandas representa uma tabela com colunas e linhas. Para a leitura consultar a estação rapidamente, extraímos a lista de IDs e montamos um dicionário que associa cada ID a latitude e altitude.

### Célula 09 — Gerar as tabelas

No notebook, esta é uma célula só; aqui ela aparece em partes para facilitar a leitura.

**Parte 1 — Quantidades e sementes.** Define quantas estações e quantas leituras gerar e reinicia as sementes, para o resultado se repetir.

```python
# Quantidade de linhas de cada tabela (volumetria da documentação). O total precisa ser de pelo menos 20.000.
N_DIM = 10_050
N_FATO = 62_000

# Reinicia as sementes: o resultado fica reproduzível.
Faker.seed(42)
random.seed(42)
```

**Parte 2 — Gerar as estações.** Chama a função de estação 10.050 vezes e monta a tabela.

```python
# ---- Tabela dimensão: usa a função gerar_linha (Célula 05) ----
tabela_dim = pd.DataFrame([gerar_linha(i) for i in range(1, N_DIM + 1)], columns=COLUNAS_DIMENSAO)
```

**Parte 3 — Gerar as leituras.** Prepara a lista de estações e a latitude e a altitude de cada uma, e chama a função de leitura 62.000 vezes.

```python
# ---- Tabela fato: usa a função gerar_linha_fato (Célula 08) ----
# ids_dim é a lista de IDs da dimensão GERADA acima: é nela que a fato busca a chave estrangeira.
ids_dim = list(tabela_dim[COLUNAS_DIMENSAO[0]])
info_dim = dict(zip(tabela_dim["estacao_id"], zip(tabela_dim["latitude"], tabela_dim["altitude_m"])))
tabela_fato = pd.DataFrame([gerar_linha_fato(i, ids_dim, info_dim) for i in range(1, N_FATO + 1)], columns=COLUNAS_FATO)
```

**Parte 4 — Ordenar e conferir.** Coloca as leituras em ordem de data, numera os IDs de novo, guarda as duas tabelas e mostra o tamanho e as primeiras linhas.

```python
# Ordena as leituras pelo momento e renumera o leitura_id (1, 2, 3...) em ordem cronológica.
tabela_fato = tabela_fato.sort_values("data_hora", kind="stable").reset_index(drop=True)
tabela_fato["leitura_id"] = range(1, len(tabela_fato) + 1)

# Guarda as tabelas usando o nome de cada uma.
BASES_GERADAS = {NOME_TABELA: tabela_dim, NOME_TABELA_FATO: tabela_fato}

print({nome: len(df) for nome, df in BASES_GERADAS.items()}, "| total:", len(tabela_dim) + len(tabela_fato))
display(tabela_dim.head())
display(tabela_fato.head())
```

As sementes são reiniciadas antes da geração definitiva. O zip combina valores na mesma ordem. A dimensão é criada primeiro; o segundo DataFrame recebe informações dela. A ordenação usa a data no formato ano-mês-dia, que permite ordem cronológica; depois renumeramos leitura_id.

> **Ponto de verificação:** o print mostra `{'estacoes': 10050, 'leituras': 62000} | total: 72050`. As duas tabelas exibidas têm sete e nove colunas, respectivamente.

A base existe, mas ainda não sabemos se ela respeita todas as regras. Isso vem antes de exportar.

## 8. Valide antes de exportar

**Objetivo:** provar, com testes, que a base segue o esquema e as regras do tema, e só então gravar os CSV.

É preciso provar que as regras foram atendidas por verificações, não apenas pela ausência de exceções. Compare nomes e ordem das colunas, unicidade dos IDs, vínculo entre as tabelas, presença de leituras para cada estação, datas e regras meteorológicas. isin verifica se cada valor pertence a um conjunto; all exige que todas as verificações sejam verdadeiras.

### Célula 10 — Validar e gravar os CSV

A célula é longa, mas faz quatro coisas em sequência. No notebook, ela continua sendo uma célula só; aqui ela aparece em partes para facilitar a leitura.

**Parte 1 — A estrutura das tabelas.** Confere se as colunas estão na ordem do esquema, se os IDs não se repetem, se toda chave estrangeira existe na dimensão e se a base tem pelo menos 20.000 linhas.

```python
# ---- Regras do guia ----
assert list(tabela_dim.columns) == COLUNAS_DIMENSAO, "Colunas diferentes do esquema na tabela dimensão"
assert list(tabela_fato.columns) == COLUNAS_FATO, "Colunas diferentes do esquema na tabela fato"
assert tabela_dim[COLUNAS_DIMENSAO[0]].is_unique, "ID repetido na tabela dimensão"
assert tabela_fato["leitura_id"].is_unique, "ID repetido na tabela fato"
assert tabela_fato[COLUNA_FK].isin(tabela_dim[COLUNAS_DIMENSAO[0]]).all(), "Chave estrangeira sem ID na dimensão"
assert len(tabela_dim) + len(tabela_fato) >= 20_000, "Abaixo do mínimo (20.000 linhas somadas)."
```

**Parte 2 — Estações, datas e faixas de valores.** Confere se toda estação tem leitura, se as leituras estão em ordem de data, se as coordenadas do Brasil estão dentro do país, se a umidade está na faixa e se só existem as 4 condições do tempo.

```python
# ---- Regras da documentação do tema ----
assert tabela_dim["estacao_id"].isin(tabela_fato["estacao_id"]).all(), "Há estação sem nenhuma leitura"
assert tabela_fato["data_hora"].is_monotonic_increasing, "Leituras fora de ordem cronológica"
lat_brasil = tabela_dim.loc[tabela_dim["pais"] == "Brasil", "latitude"]
assert lat_brasil.between(-33, 5).all(), "Latitude do Brasil fora da faixa (-33 a 5)"
assert tabela_fato["umidade_pct"].between(0, 100).all(), "Umidade fora da faixa (0 a 100)"
assert set(tabela_fato["condicao_tempo"]) <= set(CONDICOES), "Condição do tempo fora dos 4 valores válidos"
```

**Parte 3 — As regras do tempo.** Confere se só chove em leituras chuvosas ou de tempestade e se, na média, a tempestade tem menos pressão e mais umidade e vento que o tempo ensolarado.

```python
com_chuva = tabela_fato["condicao_tempo"].isin(["chuvoso", "tempestade"])
assert (tabela_fato.loc[com_chuva, "precipitacao_mm"] > 0).all(), "Leitura chuvosa ou de tempestade sem chuva"
assert (tabela_fato.loc[~com_chuva, "precipitacao_mm"] == 0).all(), "Chuva em leitura ensolarada ou nublada"

medias = tabela_fato.groupby("condicao_tempo")[["pressao_hpa", "umidade_pct", "velocidade_vento_kmh"]].mean()
assert medias.loc["tempestade", "pressao_hpa"] < medias.loc["ensolarado", "pressao_hpa"], "Tempestade deveria ter pressão menor"
assert medias.loc["tempestade", "umidade_pct"] > medias.loc["ensolarado", "umidade_pct"], "Tempestade deveria ter umidade maior"
assert medias.loc["tempestade", "velocidade_vento_kmh"] > medias.loc["ensolarado", "velocidade_vento_kmh"], "Tempestade deveria ter vento maior"
print("Todas as validações passaram.")
display(medias.round(1))
```

Para entender a Parte 3:

- `.loc[com_chuva, "precipitacao_mm"]` pega só as linhas em que com_chuva é True e, delas, só a coluna da chuva.
- O `~` inverte a marca: `~com_chuva` são as linhas ensolaradas ou nubladas.
- `groupby("condicao_tempo")` separa as leituras por condição antes de calcular. Imagine quatro leituras: ensolarado com 8 e 10 km/h, tempestade com 30 e 40 km/h. O groupby forma dois grupos, e o `.mean()` calcula a média de cada um: 9 para ensolarado e 35 para tempestade. Na base completa acontece o mesmo, só que com quatro grupos e três colunas.
- `medias.loc["tempestade", "pressao_hpa"]` lê um valor dessa tabela de médias: a linha da tempestade, na coluna da pressão.

**Parte 4 — Gravar e baixar os CSV.** Só chega aqui se todas as regras acima passaram. Grava um arquivo por tabela e lê de volta para conferir. No Colab, também baixa uma cópia para o seu computador. No computador não precisa baixar, porque os arquivos já ficam na pasta `bases_geradas`, ao lado do notebook.

```python
# ---- Grava um CSV por tabela, lê de volta para conferir e baixa o arquivo (só no Colab) ----
os.makedirs("bases_geradas", exist_ok=True)
for nome, df in BASES_GERADAS.items():
    caminho = f"bases_geradas/{nome}.csv"
    df.to_csv(caminho, index=False, encoding="utf-8")
    lido = pd.read_csv(caminho)
    assert len(lido) == len(df) and list(lido.columns) == list(df.columns), f"CSV diferente em {nome}"
    print(nome + ".csv ->", len(lido), "linhas")
    if EM_COLAB:
        files.download(caminho)
```

**Ajuste recomendado em relação ao notebook original:** sua verificação de umidade aceita 0 a 100%, mas o esquema do tema e o gerador definem 20 a 100%. Substitua a linha de validação por:
```python
assert tabela_fato["umidade_pct"].between(20, 100).all(), "Umidade fora de 20 a 100"
```
Acrescente verificações de altitude, ausência de nulos e vento não negativo antes de exportar:
```python
assert tabela_dim["altitude_m"].between(0, 3499).all()
assert not tabela_dim.isna().any().any()
assert not tabela_fato.isna().any().any()
assert tabela_fato["velocidade_vento_kmh"].ge(0).all()
```
Não corrija uma leitura errada manualmente na planilha; investigue a função que a gerou. A comparação da pressão por condição é um teste geral do gerador, mas a altitude domina essa medida; ela não demonstra causalidade. O código exporta sem o índice do pandas, relê cada arquivo e baixa uma cópia. Guarde ambos os CSV gerados para a entrega.

> **Ponto de verificação:** aparece "Todas as validações passaram.", a tabela de médias mostra tempestade com mais vento e umidade e menos pressão que ensolarado, e cada CSV informa 10.050 e 62.000 linhas. Se um assert falhar, a mensagem diz qual regra quebrou: volte à função que gera aquela coluna.

Com a base validada e salva, falta levá-la para a planilha.

## 9. Envie ao Google Sheets

**Objetivo:** gravar as duas tabelas no Google Sheets em lotes e conferir se tudo chegou.

Uma chamada por registro seria lenta e poderia atingir limites da API. Vamos enviar listas de até 5.000 registros, com pausa de dois segundos. Isso reduz as requisições, mas não garante que a quota nunca seja atingida.

### Célula 11 — Definir a função de envio

No notebook, esta é uma célula só; aqui ela aparece em partes para facilitar a leitura.

**Parte 1 — Começo da função: preparar a aba.** Usa a aba se ela já existir (apagando o conteúdo) ou cria uma nova, ajusta o tamanho e escreve o cabeçalho.

```python
def enviar(sh, df, nome_aba, lote=5000, pausa=2):
    # Formata o número com ponto como separador de milhar (ex.: 62.000).
    n = lambda x: f"{x:,}".replace(",", ".")

    # Reaproveita a aba se ela já existir (limpando o conteúdo) ou cria uma nova.
    try:
        aba = sh.worksheet(nome_aba)
        aba.clear()
    except gspread.WorksheetNotFound:
        aba = sh.add_worksheet(title=nome_aba, rows=len(df) + 1, cols=len(df.columns))

    # Ajusta o tamanho da aba e grava o cabeçalho na linha 1.
    aba.resize(rows=len(df) + 1, cols=len(df.columns))
    aba.update(range_name="A1", values=[list(df.columns)])
```

**Parte 2 — Ainda na função: enviar em lotes.** Transforma a tabela em listas e envia de 5.000 em 5.000 linhas, com uma pausa entre os lotes.

```python
    # Converte a tabela em lista de linhas (valor ausente vira texto vazio).
    valores = df.astype(object).where(df.notna(), "").values.tolist()

    # Envia em lotes, com pausa para respeitar o limite de escritas por minuto.
    for i in range(0, len(valores), lote):
        aba.append_rows(valores[i:i + lote], value_input_option="RAW")
        print(f"  {nome_aba}: {n(min(i + lote, len(valores)))} / {n(len(valores))}")
        time.sleep(pausa)
```

**Parte 3 — Fim da função: conferir.** Lê a aba de volta e para com um erro se o número de linhas não for igual ao que foi enviado.

```python
    # Lê de volta e confere a quantidade de linhas (sem contar o cabeçalho).
    lidas = len(aba.col_values(1)) - 1
    if lidas != len(df):
        raise RuntimeError(f"Aba {nome_aba}: enviei {len(df)}, li {lidas}.")
    print(f"OK {nome_aba}: {n(len(df))} linhas na planilha")
```

A função abre a aba ou cria uma nova; se ela já existir, apaga seu conteúdo, redimensiona e escreve o cabeçalho. Depois transforma a tabela em listas, envia fatias e lê a primeira coluna para conferir a quantidade.

Dois recursos de Python aparecem aqui:

- `try` / `except`: o Python tenta abrir a aba. Se ela não existir, o gspread gera o erro WorksheetNotFound; o except captura esse erro e, em vez de parar o programa, cria a aba.
- `lambda` cria uma função pequena numa linha só. Aqui, `n(62000)` devolve o texto "62.000", só para a mensagem ficar mais fácil de ler.

`valores[i:i + lote]` pega uma fatia da lista: com lote de 5.000, a primeira volta envia as linhas 0 a 4.999, a segunda, 5.000 a 9.999, e assim por diante.

> **Atenção:** a função apaga o conteúdo da aba antes de enviar. Execute somente no arquivo destinado ao desafio.

### Célula 12 — Enviar as duas tabelas

```python
# Envia cada tabela gerada para a aba de mesmo nome (dimensão e fato).
for nome, df in BASES_GERADAS.items():
    enviar(sh, df, nome)
```

A função é chamada para estacoes e leituras. Acompanhe as mensagens até a conclusão. Se ocorrer erro 429, aguarde o restabelecimento da quota; reexecute a função na aba afetada, lembrando que ela limpará e reconstruirá essa aba.

### Célula 13 — Conferir a entrega

No notebook, esta é uma célula só; aqui ela aparece em partes para facilitar a leitura.

**Parte 1 — O total de linhas.** Soma as linhas das duas tabelas e confere se passa do mínimo de 20.000.

```python
# Soma e exibe a quantidade total de linhas das tabelas.
total = sum(len(df) for df in BASES_GERADAS.values())
print("Total de linhas:", f"{total:,}".replace(",", "."))
assert total >= 20_000, "Abaixo do mínimo (20.000 linhas somadas)."
```

**Parte 2 — Cabeçalho e quantidade em cada aba.** Lê cada aba da planilha e compara o cabeçalho e o número de linhas com a tabela gerada.

```python
# Confere os cabeçalhos e a quantidade de registros em cada aba.
for nome, df in BASES_GERADAS.items():
    w = sh.worksheet(nome)
    assert w.row_values(1) == list(df.columns), f"Cabeçalho diferente em {nome}"
    assert len(w.col_values(1)) - 1 == len(df), f"Contagem diferente em {nome}"
```

**Parte 3 — A ligação entre as tabelas.** Confere se toda chave estrangeira das leituras existe na tabela de estações e mostra as abas da planilha.

```python
# Confere o relacionamento: toda chave estrangeira da tabela fato existe na tabela dimensão.
fk_fato = BASES_GERADAS[NOME_TABELA_FATO][COLUNA_FK]
ids_dim = BASES_GERADAS[NOME_TABELA][COLUNAS_DIMENSAO[0]]
assert fk_fato.isin(ids_dim).all(), "Há chave estrangeira sem ID correspondente na tabela dimensão"

print("A planilha confere com a base gerada.")
print("Abas da planilha:", [w.title for w in sh.worksheets()])
```

A célula original confere cabeçalhos e quantidades na planilha, mas sua última validação de FK usa os DataFrames locais. Para validar o relacionamento efetivamente gravado, acrescente:
```python
ids_enviados = set(sh.worksheet("estacoes").col_values(1)[1:])
fks_enviadas = sh.worksheet("leituras").col_values(2)[1:]
assert len(ids_enviados) == N_DIM
assert len(fks_enviadas) == N_FATO
assert set(fks_enviadas) <= ids_enviados
assert ids_enviados <= set(fks_enviadas)
```
Os índices 1 e 2 são os números das colunas no Sheets; [1:] ignora o cabeçalho. Abra o arquivo e confira as duas abas e os primeiros registros. Exporte a planilha como .xlsx pelo menu Arquivo > Fazer download > Microsoft Excel.

> **Ponto de verificação:** aparece "A planilha confere com a base gerada." e a lista de abas mostra estacoes e leituras.

Os dados estão gerados, validados e na planilha. Falta reunir os arquivos da entrega.

## 10. Prepare a entrega

**Objetivo:** conferir os arquivos e entregar sem publicar nenhum segredo.

Registre o uso de IA nos trechos em que foi utilizada, seguindo o formato do roteiro (AI-Generated-Date, SPDX-SnippetCopyrightText e a licença aplicável). Revise as saídas do notebook: não publique tokens, chaves, JSON ou outros segredos. No GitHub, envie o notebook executado, os CSV gerados e a cópia .xlsx. Entregue o link no Canvas.

- [ ] Notebook executado em ordem, com as duas partes.
- [ ] Teste de dez estações e conferência do cabeçalho registrados.
- [ ] As duas tabelas seguem o esquema do tema; mínimo de 20.000 linhas atendido.
- [ ] Regras de geração e relacionamento verificadas; 72.050 linhas na implementação proposta.
- [ ] CSV exportados e conferidos.
- [ ] Planilha preenchida com uma aba por tabela e validada.
- [ ] Uso de IA registrado e dados identificados como fictícios.
- [ ] Nenhuma credencial publicada; arquivos e link entregues.

## Referências do projeto

- [Roteiro oficial do desafio — parte 1](../desafio_01/README.md), [parte 2](../desafio_01/02.md), [parte 3](../desafio_01/03.md).
- [Esquema e regras do Serviço Meteorológico](../bases_dados/06-servico-meteorologico/06-servico-meteorologico.md).
- [Notebook de referência](../exercicios/Desafio1_API_Python.ipynb).
- [Diretrizes pedagógicas](../docs/DIRETRIZES_APOSTILAS.md).

O código das células foi reproduzido do notebook do projeto; os ajustes de validação foram explicitados. Os dados gerados podem diferir da base oficial dos desafios seguintes. Este guia não pressupõe a leitura de outra apostila.
