# Regressão Linear com Dados de Energia Solar (PVGIS)

**Checkpoint 02 – Parte 1: Modelos de Regressão**

Projeto de Machine Learning que estima a **potência gerada por um sistema fotovoltaico** a partir de variáveis meteorológicas e solares, usando dados horários reais obtidos da API pública do PVGIS e comparando dois modelos de Regressão Linear.

## Integrantes

| Nome | RM |
|---|---|
| André Debiazzi | 569062 |
| Kaique da Silva | 572718 |
| Vinicius Cristal | 572049 |

## Objetivo

Percorrer o fluxo completo de um problema de regressão: obter dados de uma API pública, organizá-los em um DataFrame, inspecioná-los, visualizar relações, treinar dois modelos de Regressão Linear e compará-los com **MAE, MSE e R²**.

## Fonte dos dados

- **API:** PVGIS (*Photovoltaic Geographical Information System*), do Joint Research Centre da Comissão Europeia
- **Recurso:** dados horários (`seriescalc`)
- **Endereço:** `https://re.jrc.ec.europa.eu/api/v5_3/seriescalc`
- **Localização:** Petrolina - PE (latitude -9,39; longitude -40,50)
- **Período:** 2020 a 2022 (26.304 registros horários)
- **Sistema simulado:** 1 kWp, perdas de 14%, inclinação de 10° e azimute 180 (voltado ao norte)

Não há arquivo CSV no repositório: os dados são baixados direto da API ao executar o notebook.

## Variáveis

| Variável | Descrição | Papel |
|---|---|---|
| `P` | Potência fotovoltaica (W) | **Alvo (y)** |
| `G_i` | Irradiância no plano dos módulos, `G(i)` no PVGIS (W/m²) | Entrada |
| `H_sun` | Altura do Sol (graus) | Entrada |
| `T2m` | Temperatura do ar a 2 m (°C) | Entrada |
| `WS10m` | Velocidade do vento a 10 m (m/s) | Entrada |

## Etapas do notebook

1. **Requisição à API** e conversão da resposta JSON em DataFrame (`df`).
2. **Inspeção:** dimensões, tipos, valores ausentes (nenhum) e estatísticas descritivas.
3. **Tratamento do período noturno:** foram removidos os registros com `G_i = 0` (13.324 linhas, 50,7%). Nesses horários a potência é zero por definição física, e mantê-los inflaria as métricas. Restaram **12.980 registros** (`df_dia`).
4. **Gráficos de dispersão** entre cada variável de entrada e `P`.
5. **Matriz de correlação** de Pearson.
6. **Separação treino/teste:** 80% / 20% com `random_state=42` (10.384 linhas de treino e 2.596 de teste).
7. **Modelo 1** (`modeloLR1`): `G_i`, `H_sun`, `T2m`, `WS10m`.
8. **Modelo 2** (`modeloLR2`): `G_i`, `T2m`. Usa a mesma partição do Modelo 1, o que torna a comparação justa.
9. **Tabela comparativa**, gráficos de valor real × previsto e análise final.

## Resultados

**Correlação com a potência (`P`):**

| Variável | Correlação |
|---|---|
| `G_i` | 0,998 |
| `H_sun` | 0,855 |
| `T2m` | 0,392 |
| `WS10m` | 0,127 |

**Comparação dos modelos (conjunto de teste):**

| Modelo | Variáveis utilizadas | MAE (W) | MSE (W²) | R² |
|---|---|---|---|---|
| Modelo 1 | G_i, H_sun, T2m, WS10m | 8,6513 | 113,0191 | 0,9981 |
| Modelo 2 | G_i, T2m | 9,7319 | 151,5778 | 0,9974 |

## Conclusões

- O **Modelo 1** teve melhor desempenho nas três métricas (maior R², menor MAE e menor MSE), mas a diferença é pequena: cerca de 1 W de MAE e 0,0007 de R².
- A irradiância `G_i` explica quase toda a potência, com relação praticamente linear (r = 0,998).
- `H_sun` e `G_i` são redundantes entre si (correlação de 0,857), então `H_sun` acrescenta pouco.
- O Modelo 2 usa menos variáveis e tem desempenho muito próximo, o que o torna uma opção mais simples e interpretável.
- Limitações: os valores de `P` são **simulados** pelo PVGIS (não medidos em campo), o `train_test_split` aleatório mistura horas vizinhas de uma série temporal, e o modelo só vale para horas com Sol.

## Como executar

**Requisitos:** Python 3.9+ e conexão com a internet (para consultar a API).

```bash
pip install requests numpy pandas matplotlib scikit-learn jupyter
jupyter notebook Checkpoint_02_Parte_1_Modelos_de_Regressão.ipynb
```

Execute todas as células em ordem. Também funciona no Google Colab, que já traz essas bibliotecas instaladas.

Para trocar a localização ou o período, altere as variáveis `CIDADE`, `LATITUDE`, `LONGITUDE`, `ANO_INICIO` e `ANO_FIM` na célula de configuração. Os resultados mudam de acordo com a nova consulta.

## Estrutura

```
.
├── Checkpoint_02_Parte_1_Modelos_de_Regressão.ipynb
└── README.md
```

## Tecnologias

Python, Requests, Pandas, NumPy, Matplotlib e Scikit-learn.
