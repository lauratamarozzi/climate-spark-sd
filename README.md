# Análise Global de Mudanças Climáticas — Apache Spark
### Sistemas Distribuídos | UEL | 2026

---

## Pré-requisitos

- Docker Desktop instalado e rodando
- ~4GB de RAM livres para o cluster
- Os dois datasets baixados na pasta `data/`

---

## Estrutura de Pastas

```
spark-project/
├── docker-compose.yml
├── data/
│   ├── GlobalLandTemperaturesByCity.csv   ← Kaggle (Berkeley Earth)
│   └── owid-co2-data.csv                 ← Our World in Data (GitHub)
├── notebooks/
│   └── climate_analysis.ipynb
└── output/                               ← Criada automaticamente
```

---

## Passo a Passo

### 1. Baixar os datasets

**Dataset de temperatura:**
- Acesse: https://www.kaggle.com/datasets/berkeleyearth/climate-change-earth-surface-temperature-data
- Baixe `GlobalLandTemperaturesByCity.csv` (~500MB)
- Coloque em `spark-project/data/`

**Dataset de CO2:**
- Acesse: https://raw.githubusercontent.com/owid/co2-data/master/owid-co2-data.csv
- Salve como `owid-co2-data.csv`
- Coloque em `spark-project/data/`

---

### 2. Criar as pastas necessárias

No terminal (PowerShell ou WSL):

```bash
mkdir -p spark-project/data
mkdir -p spark-project/notebooks
mkdir -p spark-project/output
```

Copie o `docker-compose.yml` para `spark-project/` e o notebook para `spark-project/notebooks/`.

---

### 3. Subir o cluster

```bash
cd spark-project
docker-compose up -d
```

Aguarde ~2 minutos. Para verificar se subiu:

```bash
docker-compose ps
```

Todos os 4 serviços devem estar `Up`.

---

### 4. Acessar as interfaces

| Interface | URL |
|---|---|
| **Jupyter Lab** | http://localhost:8888 |
| **Spark Master UI** | http://localhost:8080 |
| **Spark App UI (DAG)** | http://localhost:4040 |
| **Worker 1** | http://localhost:8081 |
| **Worker 2** | http://localhost:8082 |

> O token do Jupyter aparece no log: `docker-compose logs jupyter`

---

### 5. Executar o notebook

1. Abra http://localhost:8888
2. Navegue para `work/climate_analysis.ipynb`
3. Execute célula por célula (Shift+Enter)
4. Os gráficos são salvos automaticamente em `output/`

---

### 6. Evidências para o relatório (prints obrigatórios)

Durante a execução, tire prints de:

- **Spark Master UI** (http://localhost:8080) → mostra os 2 workers conectados
- **Spark App UI** (http://localhost:4040) → aba "Stages" → DAG dos jobs pesados
  - Capture especialmente o DAG do Join (Pergunta 6) e do groupBy + stddev (Pergunta 3)
- **Aba "Storage"** do Spark UI → mostra o DataFrame em cache após o `.cache()`

---

### 7. Desligar o cluster

```bash
docker-compose down
```

---

## Outputs gerados

| Arquivo | Conteúdo |
|---|---|
| `output/p1_temperatura_por_decada.png` | Gráfico de linha — aquecimento por década |
| `output/p3_cidades_instabilidade.png` | Barras — cidades com maior desvio padrão |
| `output/p4_correlacao_tropical.png` | Dispersão — temp. mínima vs máxima tropical |
| `output/p6_co2_vs_temperatura.png` | Dispersão — CO2 vs temperatura (Pearson) |
| `output/p7_aceleracao_termica.png` | Barras — aceleração térmica por país |
| `output/p8_previsao_mllib.png` | Linha — histórico + previsão MLlib |
| `output/climate_co2_final.parquet` | Dataset final particionado por país |
