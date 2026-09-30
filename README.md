# 📊 Análise de Receita e Comportamento de Clientes — Megaline

Projeto de análise de dados desenvolvido para a **Megaline**, com o objetivo de comparar os planos pré-pagos **Surf** e **Ultimate** e identificar padrões de consumo, geração de receita e diferenças entre regiões.

A análise utiliza dados de **500 clientes**, referentes a chamadas, mensagens, sessões de internet, planos contratados e localização dos usuários ao longo de **2018**, combinando análise exploratória, agregações mensais e testes de hipóteses estatísticas.

---

## 🎯 Objetivo do Projeto

O departamento comercial da Megaline precisa entender qual plano apresenta maior geração de receita e como os clientes utilizam os serviços disponíveis.

A análise busca responder principalmente:

- Qual plano gera maior receita média por usuário?
- Como os clientes utilizam **chamadas, mensagens e internet** em cada plano?
- Quanto da receita vem da mensalidade e quanto vem do consumo excedente?
- Existe diferença estatisticamente significativa entre as receitas dos planos?
- A receita média dos clientes da região **NY-NJ** difere das demais regiões?

---

## 🧠 Abordagem / Arquitetura Técnica

O projeto foi desenvolvido em um **Jupyter Notebook**, seguindo um fluxo de preparação, transformação, agregação, análise exploratória e inferência estatística.

### 1. Carregamento e preparação dos dados

Foram utilizados cinco conjuntos de dados:

- `megaline_users.csv` — informações cadastrais e plano dos usuários;
- `megaline_calls.csv` — registros de chamadas;
- `megaline_messages.csv` — registros de mensagens;
- `megaline_internet.csv` — sessões de utilização de internet;
- `megaline_plans.csv` — condições comerciais dos planos.

Os principais tratamentos realizados foram:

- conversão de `user_id` para `string`;
- conversão de colunas de data para `datetime`;
- conversão de valores monetários para `float`;
- criação da variável `mes` para permitir análises mensais;
- arredondamento das chamadas para o próximo minuto inteiro, conforme a regra de cobrança;
- conversão do consumo de internet de MB para GB;
- arredondamento do tráfego excedente para o próximo GB inteiro.

### 2. Agregação mensal

Os dados de utilização foram agregados por:

```text
user_id + mes
```

Foram calculadas as seguintes métricas:

- quantidade de chamadas;
- minutos consumidos;
- quantidade de mensagens;
- volume de internet utilizado.

Em seguida, os dados foram combinados com as informações cadastrais e comerciais dos usuários.

Para preservar registros provenientes de diferentes fontes, o notebook utiliza `merge(..., how='outer')` nas agregações de consumo.

Valores ausentes nas métricas de utilização foram preenchidos com `0`, representando meses em que o usuário não utilizou determinado serviço.

### 3. Cálculo da receita

A receita mensal foi calculada considerando:

```text
Receita total =
    mensalidade do plano
    + cobranças por minutos excedentes
    + cobranças por mensagens excedentes
    + cobranças por internet excedente
```

O cálculo considera apenas o consumo que ultrapassa os limites contratados.

Para a internet, o consumo excedente é convertido de MB para GB e arredondado para cima antes da aplicação da tarifa correspondente.

### 4. Análise exploratória

Foram utilizados:

- tabelas de estatísticas descritivas;
- médias;
- variâncias;
- desvios padrão;
- gráficos de barras;
- histogramas;
- boxplots.

A análise foi realizada separadamente para **Surf** e **Ultimate**, considerando chamadas, mensagens, internet e receita.

### 5. Testes de hipóteses

#### Receita: Surf x Ultimate

Foi utilizado um **teste t de uma amostra (`st.ttest_1samp`)**, comparando a receita do plano Surf com a receita média de referência do Ultimate, de **US$ 72,31**.

Parâmetros utilizados:

```text
α = 0,05
Teste bicaudal
p-valor = 2,0618620636875684e-16
```

O resultado levou à rejeição da hipótese nula adotada no notebook, indicando diferença estatisticamente significativa entre a receita média do Surf e o valor de referência utilizado para o Ultimate.

#### Receita: NY-NJ x demais regiões

Para comparar as receitas entre os dois grupos regionais, foi aplicado inicialmente o **teste de Levene** para avaliar a igualdade das variâncias.

Resultado:

```text
Estatística = 5,4286
p-valor = 0,0199
```

Como o p-valor ficou abaixo de 0,05, as variâncias foram consideradas diferentes e, em seguida, foi aplicado o **teste t para duas amostras independentes**, com `equal_var=False`.

Resultado:

```text
p-valor = 0,10065559377767885
```

Nesse teste, não foi rejeitada a hipótese nula de igualdade das médias de receita entre NY-NJ e as demais regiões.

---

## 📁 Estrutura do Repositório

Estrutura observada no projeto:

```text
megaline_telecomunicacoes/
│
├── datasets/
│   ├── megaline_calls.csv
│   ├── megaline_internet.csv
│   ├── megaline_messages.csv
│   ├── megaline_plans.csv
│   └── megaline_users.csv
│
├── notebooks/
│   └── notebook.ipynb
│
└── requirements.txt
```

### Principais diretórios e arquivos

| Caminho | Descrição |
|---|---|
| `datasets/` | Conjuntos de dados utilizados na análise. |
| `notebooks/` | Jupyter Notebook contendo todo o processo de análise, tratamento, visualização e testes estatísticos. |
| `requirements.txt` | Arquivo destinado às dependências necessárias para executar o projeto. |

---

## ⚙️ Instalação e Execução

### 1. Clonar o repositório

```bash
git clone https://github.com/alexpereira951/magaline_telecomunicacoes
cd megaline_telecomunicacoes
```

### 2. Criar um ambiente virtual

```bash
python -m venv .venv
```

Ative o ambiente virtual:

**Windows:**

```bash
.venv\Scripts\activate
```

**Linux/macOS:**

```bash
source .venv/bin/activate
```

### 3. Instalar as dependências

```bash
pip install -r requirements.txt
```

### 4. Executar o Jupyter Notebook

```bash
jupyter notebook
```

Depois, abra:

```text
notebooks/notebook.ipynb
```

> **Observação:** o notebook utiliza caminhos relativos para acessar os arquivos da pasta `datasets/`. Portanto, mantenha a estrutura de diretórios do projeto ao executar a análise.

---

## 🛠️ Stack Tecnológica

| Tecnologia | Utilização |
|---|---|
| 🐍 **Python** | Linguagem principal do projeto |
| 🐼 **Pandas** | Manipulação, transformação e agregação dos dados |
| 🔢 **NumPy** | Operações numéricas e estatísticas auxiliares |
| 📊 **Matplotlib** | Construção das visualizações |
| 📈 **Seaborn** | Boxplots e visualizações estatísticas |
| 🧪 **SciPy** | Testes estatísticos de hipóteses |
| 📓 **Jupyter Notebook** | Desenvolvimento e documentação da análise |

---

## 📊 Principais Resultados

### 💰 Receita por plano

A análise descritiva encontrou as seguintes receitas médias mensais por usuário:

| Plano | Receita média | Desvio padrão |
|---|---:|---:|
| **Surf** | US$ 60,71 | US$ 55,39 |
| **Ultimate** | US$ 72,31 | US$ 11,40 |

A diferença observada é de aproximadamente **US$ 11,60 por usuário/mês**.

O teste realizado no notebook apresentou:

```text
p-valor = 2,0618620636875684e-16
```

Esse resultado é inferior ao nível de significância de 5%, levando à rejeição da hipótese nula definida na análise.

Um aspecto relevante é a diferença de dispersão: a receita do **Surf** apresenta maior variabilidade, enquanto a receita do **Ultimate** permanece mais concentrada próxima ao valor da mensalidade.

### 📞 Chamadas

As médias mensais de minutos utilizados foram muito próximas:

| Plano | Média de minutos |
|---|---:|
| **Surf** | 428,75 |
| **Ultimate** | 430,45 |

Apesar da proximidade das médias, os padrões de utilização diferem em relação aos limites dos planos. Usuários do Surf apresentam ocorrências de consumo acima dos **500 minutos** incluídos, enquanto os usuários do Ultimate utilizam uma parcela menor dos **3.000 minutos** disponíveis.

### 💬 Mensagens

| Plano | Média de mensagens | Desvio padrão |
|---|---:|---:|
| **Surf** | 31,16 | 33,57 |
| **Ultimate** | 37,55 | 34,77 |

O consumo de mensagens apresenta comportamento semelhante entre os planos, com diferença relativamente pequena entre as médias.

### 🌐 Internet

O consumo médio mensal encontrado foi:

| Plano | Média de internet |
|---|---:|
| **Surf** | 16,17 GB |
| **Ultimate** | 16,81 GB |

As médias são próximas, indicando comportamento de consumo de internet semelhante entre os grupos analisados.

### 🗺️ Diferenças regionais

Para a comparação entre **NY-NJ** e as demais regiões:

```text
Teste de Levene
p-valor = 0,0199

Teste t para amostras independentes
p-valor = 0,1007
```

Com `α = 0,05`, o teste final não rejeitou a hipótese nula de igualdade das médias de receita entre os dois grupos regionais analisados.

---

## ⚠️ Limitações

O projeto apresenta algumas limitações que devem ser consideradas na interpretação dos resultados:

1. **Período de análise limitado:** os dados utilizados correspondem ao ano de **2018**, portanto os padrões de consumo e receita observados representam apenas esse período e podem não refletir comportamentos atuais.

2. **Ausência de variáveis externas:** a análise considera principalmente dados de utilização dos serviços e informações dos clientes, sem incorporar fatores externos como concorrência, campanhas promocionais, sazonalidade comercial ou alterações de mercado que poderiam influenciar o consumo.

3. **Diferenças na quantidade de dados entre os planos:** a distribuição de clientes entre **Surf** e **Ultimate** não é necessariamente equilibrada, o que pode influenciar a comparação das estatísticas descritivas e da variabilidade observada entre os grupos.

4. **Escopo estatístico dos testes:** os testes de hipóteses realizados avaliam especificamente as comparações definidas no projeto e dependem das premissas dos métodos estatísticos utilizados. Portanto, seus resultados não devem ser generalizados para outros períodos, populações ou cenários sem uma análise adicional.

> Os resultados devem, portanto, ser interpretados dentro do contexto do conjunto de dados, período analisado e escopo metodológico definidos neste projeto.

## 🔎 Insights de Negócio

A análise evidencia alguns padrões relevantes para a área comercial:

- O **Ultimate apresentou maior receita média mensal por usuário** no conjunto analisado.
- O **Surf possui maior variabilidade de receita**, associada principalmente ao consumo excedente.
- O consumo médio de **minutos, mensagens e internet** é relativamente próximo entre os planos, apesar das diferenças nos limites incluídos.
- Usuários do Surf podem gerar cobranças adicionais ao ultrapassar os limites contratados.
- O Ultimate apresenta receita mais concentrada próxima à mensalidade, enquanto o Surf apresenta maior dispersão.
- A análise estatística realizada não identificou diferença significativa entre as receitas médias de **NY-NJ** e das demais regiões, considerando o nível de significância de 5%.

---

## 🧪 Conclusão

O projeto combina **engenharia de dados, análise exploratória e estatística inferencial** para transformar registros brutos de utilização em métricas de consumo e receita por cliente e por mês.

O fluxo construído no notebook demonstra, de ponta a ponta:

```text
Dados brutos
    ↓
Tratamento e padronização
    ↓
Enriquecimento temporal
    ↓
Agregação por usuário/mês
    ↓
Integração dos datasets
    ↓
Cálculo de consumo excedente
    ↓
Cálculo de receita
    ↓
Análise exploratória
    ↓
Testes estatísticos
    ↓
Insights de negócio
```

O principal resultado observado é que o **plano Ultimate apresentou receita média superior ao Surf**, enquanto os padrões de consumo de serviços foram relativamente semelhantes entre os usuários dos dois planos. A análise regional, por sua vez, não encontrou evidência estatística suficiente para afirmar diferença entre as receitas médias de NY-NJ e das demais regiões.

---

## 📌 Projeto de Portfólio

Este projeto demonstra competências práticas em:

- **ETL e preparação de dados**;
- **Pandas e manipulação de DataFrames**;
- **integração de múltiplas fontes de dados**;
- **agregação temporal e criação de métricas**;
- **engenharia de variáveis**;
- **cálculo de receita baseado em regras de negócio**;
- **análise exploratória de dados (EDA)**;
- **visualização estatística**;
- **testes de hipóteses com SciPy**;
- **interpretação de resultados orientada ao negócio**.

