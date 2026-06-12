# 📈 Forecasting do Índice Bovespa (^BVSP)

---

## 📌 Descrição

Aplicação do pipeline completo de análise de séries temporais sobre o índice Bovespa (^BVSP), com dados diários de **2018 a 2024** (1.737 observações). O projeto compara três abordagens de forecasting para prever os **últimos 30 dias** da série.

### Propósito do projeto

O objetivo não era "prever o mercado" — isso é reconhecidamente impossível com modelos simples. O propósito foi aplicar o **pipeline completo de séries temporais** em um dado real e relevante, exercitando cada etapa: coleta, exploração, diagnóstico de estacionariedade, identificação de estrutura via ACF/PACF, modelagem e avaliação comparativa com métricas padronizadas.

A escolha do Ibovespa foi intencional: séries financeiras são um caso clássico de alta dificuldade, o que torna o exercício mais rico — cada decisão metodológica precisa ser justificada e os resultados precisam ser interpretados com honestidade.

---

## 🗂️ Estrutura do Repositório
├── ibovespa_forecasting_final.ipynb   # Notebook completo com todo o pipeline
├── ibovespa_pitch.pptx                # Slides da apresentação final
└── README.md

---

## 🔬 Pipeline

| Etapa | Descrição |
|---|---|
| **Coleta** | Download via `yfinance` — ticker `^BVSP` |
| **EDA** | Série, média/desvio móveis, log-retorno, outliers |
| **Decomposição** | `seasonal_decompose` multiplicativa (período = 252 dias úteis) |
| **Estacionariedade** | Teste ADF — série original e log-retorno |
| **ACF / PACF** | Diagnóstico da estrutura de autocorrelação |
| **Modelos** | Baseline (MM21), ARIMA(1,1,1) e Prophet |
| **Avaliação** | MAE, RMSE e MAPE no período de teste (30 dias) |

---

## 📊 Resultados

| Modelo | MAE | RMSE | MAPE (%) |
|---|---|---|---|
| 🥇 **ARIMA(1,1,1)** | 2.715 | **3.674** | **2,21%** |
| 🥈 Baseline (MM21) | 4.385 | 5.293 | 3,55% |
| 🥉 Prophet | 10.822 | 11.666 | 8,71% |

O **ARIMA(1,1,1)** obteve o menor erro em todas as métricas, superando o baseline em ~31% e o Prophet em ~68% no RMSE.

---

## 💡 Principais Achados

- O Ibovespa **não é estacionário** em nível (ADF p=0,13), mas o **log-retorno é estacionário** (ADF p≈0,000)
- ACF e PACF cortam rapidamente → **random walk confirmado**: o passado explica pouco o futuro
- O **Prophet falhou** porque sazonalidades semanais/anuais não são estáveis em séries financeiras
- Em séries tipo random walk, **modelos conservadores superam modelos complexos**
- Maior evento identificado: colapso de **−15,99%** em 12/03/2020 (COVID), seguido de rebote de **+13,02%** no dia seguinte

---
### Por que o ARIMA era esperado vencer em dados financeiros

Mercados financeiros eficientes seguem aproximadamente um **random walk**: o melhor palpite para o valor de amanhã é o valor de hoje, mais um erro aleatório imprevisível. Isso tem uma implicação direta na escolha do modelo.

O ARIMA(1,1,1) com `d=1` essencialmente modela exatamente esse comportamento — a diferenciação remove a tendência e o que sobra tem pouca estrutura autocorrelacionada, como confirmado pelo ACF/PACF. Sua estratégia é conservadora: **não tenta projetar tendências nem sazonalidades**, apenas segue de perto o último nível observado.

O Prophet, por outro lado, foi projetado para séries de negócio (vendas, tráfego, demanda) onde sazonalidades semanais e anuais são **estáveis e repetíveis**. Em dados financeiros, esses padrões não existem de forma confiável — o mercado é movido por eventos exógenos (decisões de juros, eleições, crises) que nenhuma sazonalidade captura. Resultado: o Prophet "apostou" em padrões que não se confirmaram e acumulou erro sistematicamente.

Em resumo: **para random walks, vence o modelo que menos arrisca**. O ARIMA não venceu por ser sofisticado — venceu por ser o modelo certo para o problema certo.

## 🛠️ Stack
Python 3        pandas · NumPy · Matplotlib
yfinance        coleta de dados financeiros
statsmodels     ARIMA, ADF, ACF/PACF, decomposição
prophet         modelo aditivo Meta/Facebook
scikit-learn    métricas MAE, RMSE, MAPE

---

## ▶️ Como Executar

1. Abra o notebook no Google Colab
2. Execute a primeira célula para instalar as dependências (`yfinance`, `prophet`)
3. Execute todas as células em ordem

---
