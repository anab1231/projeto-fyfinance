Código fonte em python
import pandas as pd
import yfinance as yf
from openpyxl import load_workbook
from openpyxl.chart import LineChart, Reference
# Configuração
ativo = "PETR4.SA"
periodo = "6mo"
# Baixar dados
df = yf.download(ativo, period=periodo)
# Limpeza de dados
if isinstance(df.columns, pd.MultiIndex):
 df.columns = df.columns.get_level_values(0)
df.reset_index(inplace=True)
# Simulação de Investimento
aporte_mensal = 500
cotas = 0
carteira = []
for i in range(len(df)):
 preco = float(df["Close"].iloc[i])

 if i % 21 == 0:
 cotas += aporte_mensal / preco
 carteira.append(cotas * preco)
df["Carteira"] = carteira
# Salvar no Excel
arquivo = "relatorio_financeiro.xlsx"
df.to_excel(arquivo, index=False)
# Criar os Gráficos dentro do Excel
wb = load_workbook(arquivo)
ws = wb.active
max_linha = len(df) + 1
# Gráfico de Preço
graf1 = LineChart()
graf1.title = "Preço do Ativo"
dados = Reference(ws, min_col=2, min_row=1, max_row=max_linha)
datas = Reference(ws, min_col=1, min_row=2, max_row=max_linha)
graf1.add_data(dados, titles_from_data=True)
graf1.set_categories(datas)
ws.add_chart(graf1, "H2")
wb.save(arquivo)
print("Arquivo Excel criado com sucesso!")

2. Código da Análise Financeira (Gráficos na tela)
Este é o que faz os cálculos de risco (RSI, Sharpe) e mostra os gráficos coloridos na tela do 
computador.
import pandas as pd
import numpy as np
import yfinance as yf
import matplotlib.pyplot as plt
# Configuração
ativo = "PETR4.SA"
df = yf.download(ativo, period="1y")
# Limpeza
if isinstance(df.columns, pd.MultiIndex):
 df.columns = df.columns.get_level_values(0)
df.dropna(inplace=True)
# Indicadores (Médias e RSI)
df["MM20"] = df["Close"].rolling(20).mean()
df["MM50"] = df["Close"].rolling(50).mean()
# Cálculo de Retorno e Risco
df["Retorno"] = df["Close"].pct_change()
volatilidade = df["Retorno"].std() * np.sqrt(252)

sharpe = (df["Retorno"].mean() * 252) / volatilidade
print(f"Volatilidade: {volatilidade:.4f}")
print(f"Índice Sharpe: {sharpe:.4f}")
# Gerar Gráfico de Preço
plt.figure(figsize=(10,5))
plt.plot(df["Close"], label="Preço")
plt.plot(df["MM20"], label="Média 20 dias")
plt.title("Análise de Preço - PETR4")
plt.legend()
plt.show()
