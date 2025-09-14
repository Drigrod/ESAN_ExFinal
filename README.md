# ESAN_ExFinal_LOTI_PERU
# Lottery MLOps – Prueba

Caso ficticio para una empresa de loterías. Incluye **DataOps + DevOps** y un modelo XGBoost para clasificar apuestas sospechosas.

## 🌳 Estructura
```
lottery_mlops_prueba/
├─ data/
│  └─ lottery_apuestas.csv
├─ src/
│  ├─ train_xgb.py
│  └─ app.py
├─ notebooks/
│  ├─ 01_ingest_to_delta.py
│  └─ 02_features.py
├─ docker/
│  └─ Dockerfile
├─ .github/workflows/
│  └─ ci.yml
├─ models/           # se genera tras entrenar
└─ requirements.txt
```

## 🚀 Paso a paso – GitHub (DevOps)
1. **Crear repo** en GitHub (ej. `lottery-mlops-prueba`) y subir el contenido de esta carpeta.
2. **Instalar dependencias (local):**
   ```bash
   pip install -r requirements.txt
   ```
3. **Entrenar el modelo:**
   ```bash
   python src/train_xgb.py
   # genera models/xgb_pipeline.pkl y models/metrics.json
   ```
4. **Levantar la API (FastAPI):**
   ```bash
   uvicorn src.app:app --reload
   # POST http://127.0.0.1:8000/predict
   ```
5. **CI (GitHub Actions):** el workflow `.github/workflows/ci.yml` ejecuta un smoke training y construye la imagen Docker en cada push/PR.

## 🐳 Docker (opcional)
```bash
docker build -t lottery-mlops:latest -f docker/Dockerfile .
docker run -p 8000:8000 lottery-mlops:latest
```

## 🔄 Paso a paso – Databricks (DataOps)
1. **Repos →** conecta este repo de GitHub.
2. **Sube el dataset** a DBFS: `dbfs:/FileStore/lottery/data/lottery_apuestas.csv`
3. **Ejecuta**:
   - `notebooks/01_ingest_to_delta.py` → crea `default.lottery_bets_delta` (Delta Lake; versionado/time travel).
   - `notebooks/02_features.py` → crea `default.user_features` (promedios, frecuencias, riesgos).
4. **Evidencias sugeridas** para el informe:
   - Captura de tabla Delta (SHOW HISTORY).
   - Captura de features creadas.
   - `models/metrics.json` con métricas del entrenamiento.
   - Screenshot del workflow de GitHub Actions.

## 📄 Endpoint de ejemplo
```bash
curl -X POST "http://127.0.0.1:8000/predict" -H "Content-Type: application/json" -d '{
  "user_id": 1234,
  "channel": "android",
  "stake_amount": 320.5,
  "minutes_before_close": 3,
  "bets_last_7d": 18,
  "win_rate_last_30d": 0.22,
  "ip_risk": 1,
  "geo_risk": 0,
  "num_picks": 6
}'
```

## 🔎 Notas
- Este dataset es **sintético** y solo para fines académicos.
- Ajusta hiperparámetros en `src/train_xgb.py` según tus métricas y hardware.

_Última actualización: 2025-09-14_
