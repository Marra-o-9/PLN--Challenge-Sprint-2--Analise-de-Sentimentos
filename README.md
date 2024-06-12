# Challenge Sprint 2: Processamento de Linguagem Natural

Integrantes:<br><br>
Henrique Marra Barbosa - RM97672<br>
Arthur Hieda Cunha - RM551882<br>
Lucas Bueno Taets Gustavo - RM552162<br>
Kauã Tateishi dos Santos - RM551632

---

## Análise de Sentimentos com Dash

Este projeto utiliza a biblioteca Dash para criar uma aplicação web interativa que analisa sentimentos em comentários de clientes. O código processa uma lista de comentários de exemplo, analisa o sentimento de cada comentário e exibe os resultados em um gráfico interativo.

### Passo a Passo

#### 1. Preparação do Ambiente

Certifique-se de ter Python instalado em seu sistema. Este projeto foi desenvolvido com Python 3.11. Além disso, você precisará instalar as seguintes bibliotecas Python:

- dash
- pandas
- plotly
- textblob

Instale as bibliotecas necessárias e prepare o ambiente executando as seguintes linhas em uma célula Jupyter:

```python
!pip install -U textblob
!python -m textblob.download_corpora
!pip install dash
!pip install plotly
!pip install pandas
```

#### 2. Importação das Bibliotecas

Importe as bibliotecas necessárias:

```python
from textblob import Blobber
import pandas as pd
import dash
from dash import dcc, html
import plotly.express as px
```

#### 3. Função para Ler os Comentários do Arquivo

Defina a função para ler os comentários de um arquivo de texto:

```python
# Função para ler os comentários do arquivo
def ler_comentarios(arquivo):
    with open(arquivo, "r", encoding="utf-8") as file:
        comentarios = [linha.strip() for linha in file.readlines()]
    return comentarios

# Ler os comentários do arquivo
comentarios = ler_comentarios("comentarios.txt")
print(comentarios)
```

#### 4. Função para Analisar o Sentimento dos Comentários

Defina a função para analisar os sentimentos dos comentários:

```python
# Função para analisar o sentimento de cada comentário
tb = Blobber()

def analisar_sentimento(comentarios):
    resultados = []
    for comentario in comentarios:
        blob = tb(comentario)
        polaridade = blob.sentiment.polarity
        if polaridade > 0:
            resultado = "Positivo"
        elif polaridade < 0:
            resultado = "Negativo"
        else:
            resultado = "Neutro"
        resultados.append((comentario.strip(), resultado))
    return resultados

# Analisar os sentimentos dos comentários
resultados_sentimento = analisar_sentimento(comentarios)
print(resultados_sentimento)
```

#### 5. Conversão dos Resultados para DataFrame

Converta os resultados para um DataFrame do pandas:

```python
# Converter resultados para DataFrame
df = pd.DataFrame(resultados_sentimento, columns=["Comentario", "Sentimento"])
df.head()
```

#### 6. Função para Gerar o Relatório

Defina a função para gerar um relatório dos comentários:

```python
# Função para gerar o relatório
def gerar_relatorio(df):
    positivos = df[df['Sentimento'] == "Positivo"]
    negativos = df[df['Sentimento'] == "Negativo"]
    neutros = df[df['Sentimento'] == "Neutro"]

    print(f"----- Melhores Serviços: {len(positivos)} -----")
    for comentario in positivos['Comentario']:
        print(comentario)

    print(f"\n----- Piores Serviços: {len(negativos)} -----")
    for comentario in negativos['Comentario']:
        print(comentario)

    print(f"\n----- Comentários Neutros: {len(neutros)} -----")
    for comentario in neutros['Comentario']:
        print(comentario)

# Gerar o relatório
gerar_relatorio(df)
```

#### 7. Criação do Dashboard com Dash

Crie a aplicação Dash:

```python
# Inicializar a aplicação Dash
app = dash.Dash(__name__)

# Função para gerar figuras de barras
def criar_grafico_sentimentos(df):
    contagem_sentimentos = df['Sentimento'].value_counts().reset_index()
    contagem_sentimentos.columns = ['Sentimento', 'count']
    fig = px.bar(contagem_sentimentos, x='Sentimento', y='count')
    fig.update_layout(title='Distribuição dos Sentimentos',
                      xaxis_title='Sentimento',
                      yaxis_title='Quantidade')
    return fig

# Layout da aplicação
app.layout = html.Div(children=[
    html.H1(children='Análise de Sentimentos dos Comentários'),

    dcc.Graph(
        id='grafico-sentimentos',
        figure=criar_grafico_sentimentos(df)
    ),

    html.Div(children='''
        Dashboard para visualização de sentimentos dos comentários.
    '''),
])
```

#### 8. Executar o Dashboard

Execute a aplicação Dash:

```python
# Rodar o servidor
if __name__ == '__main__':
    app.run_server(debug=True)
```
