# Network Security — Phishing URL Detection

An end-to-end machine learning pipeline that detects phishing/malicious network traffic from structured data, served through a FastAPI web app. The project covers the full lifecycle: data ingestion from MongoDB, validation, transformation, model training with experiment tracking, and a web UI for batch predictions on uploaded CSV files.

## How it works

1. **Data ingestion** — pulls raw records from a MongoDB collection into the pipeline.
2. **Data validation** — checks incoming data against a defined schema (`data_schema/`) before it's used.
3. **Data transformation** — cleans and transforms features for training.
4. **Model training** — trains a scikit-learn classifier and tracks experiments with MLflow / DagsHub.
5. **Model export** — the trained model and preprocessor are saved to `final_model/` (`model.pkl`, `preprocessor.pkl`).
6. **Serving** — a FastAPI app loads the trained model and exposes routes to retrain (`/train`) and to run batch predictions on an uploaded CSV (`/predict`), rendering results in the browser.

## Tech stack

- **Backend / API:** FastAPI, Uvicorn
- **ML:** scikit-learn, pandas, numpy, scipy
- **Experiment tracking:** MLflow, DagsHub
- **Database:** MongoDB (via PyMongo)
- **Templating:** Jinja2 (`templates/`)
- **Deployment:** Docker, GitHub Actions (CI/CD workflow in `.github/workflows`)

## Project structure

```
network-security/
├── app.py                  # FastAPI app — routes for home, train, predict
├── main.py                 # Entry point for running the training pipeline directly
├── push_data.py             # Script to push raw data into MongoDB
├── networksecurity/         # Core package: exceptions, logging, pipeline, ML utils
├── data_schema/              # Schema used to validate incoming data
├── Network_Data/             # Raw / source data
├── final_model/               # Trained model + preprocessor artifacts (model.pkl, preprocessor.pkl)
├── prediction_output/          # CSV outputs from /predict requests
├── valid_data/                  # Data that has passed validation
├── templates/                    # Jinja2 HTML templates (index, results)
├── logs/                          # Pipeline run logs
├── Dockerfile
├── requirements.txt
└── setup.py
```

## Getting started

### Prerequisites

- Python 3.8+
- A MongoDB connection string (Atlas or self-hosted)
- (Optional) A DagsHub/MLflow account if you want to track training runs

### 1. Clone and install

```bash
git clone https://github.com/niraj-codes01/network-security.git
cd network-security
pip install -r requirements.txt
```

### 2. Configure environment variables

Create a `.env` file in the project root:

```
MONGODB_URL_KEY=your_mongodb_connection_string
```

### 3. (Optional) Load data into MongoDB

```bash
python push_data.py
```

### 4. Run the app

```bash
python app.py
```

The app starts on `http://127.0.0.1:8000` (the server binds to `0.0.0.0` internally so it's reachable on your network — use `127.0.0.1` or `localhost` in your browser).

### 5. Train a model

Visit `/train`, or run the pipeline directly:

```bash
python main.py
```

This runs ingestion → validation → transformation → training, and saves the resulting model and preprocessor to `final_model/`.

### 6. Run a prediction

From the home page, upload a CSV of network/traffic records. The app loads the trained model, scores each row, and displays the results in a table — also saved to `prediction_output/output.csv`.

## Running with Docker

```bash
docker build -t network-security .
docker run -p 8000:8000 --env-file .env network-security
```

## Notes for contributors

- Trained artifacts (`final_model/`), logs, and `mlflow.db` are run outputs, not source — consider keeping them out of version control once the pipeline is stable.
- See `data_schema/` before changing input columns; predictions depend on the schema staying in sync with the model.

## License

No license has been specified yet. Add a `LICENSE` file if you'd like others to reuse this code.
