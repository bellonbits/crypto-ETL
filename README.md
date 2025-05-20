# Cryptocurrency ETL Pipeline

A Python-based ETL (Extract, Transform, Load) solution for cryptocurrency price tracking. This project integrates with exchange APIs like Coinbase to collect, process, and store cryptocurrency data in PostgreSQL.

## Features

- **Real-time price data** from major exchanges (Coinbase, etc.)
- **Flexible data transformations** for analytics and reporting
- **Multi-destination storage** in PostgreSQL 
- **Notebook-first development** for easy visualization and debugging

## Requirements

- Python 3.8+
- Jupyter Notebooks environment
- PostgreSQL database

## Installation

```bash
# Clone the repository
git clone https://github.com/yourusername/crypto-ETL.git
cd crypto-ETL

# Create and activate virtual environment
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate

# Install dependencies
pip install -r requirements.txt

# Set up configuration
cp config.example.py config.py
# Edit config.py with your API keys and database credentials
```

## Project Structure

```
crypto-etl-pipeline/
├── notebooks/
│   ├── 01_coinbase_extraction.ipynb
│ 
│   ├── 02_data_transformation.ipynb
│   └── 03_combined_pipeline.ipynb
├── src/
│   ├── extractors/
│   │   ├── __init__.py
│   │   ├── coinbase.py
│   │  
│   ├── transformers/
│   │   ├── __init__.py
│   │   └── price_transforms.py
│   ├── loaders/
│   │   ├── __init__.py
│   │   ├── postgres.py
│   │   
│   └── utils/
│       ├── __init__.py
│       └── helpers.py
├── config.py
├── requirements.txt
└── README.md
```

## Usage

### Development in Notebooks

The project is designed for notebook-first development, allowing for interactive exploration and visualization of data.

1. Start with the extraction notebooks to test API connections
2. Experiment with transformations in the transformation notebook
3. Test the full pipeline in the combined notebook

```python
# Example of the full pipeline in notebook
from src.extractors.coinbase import fetch_coinbase_prices
from src.transformers.price_transforms import transform_price_data
from src.loaders.postgres import load_to_postgres
from src.loaders.bigquery import load_to_bigquery
import config

# Extract
price_data = fetch_coinbase_prices(config.CRYPTO_PAIRS)

# Transform
transformed_prices = transform_price_data(price_data)

# Load
load_to_postgres(transformed_prices, "crypto_prices", config.PG_CONNECTION)
```

### Production Deployment

For production use, the notebook code can be:

1. Refactored into the modular structure in the `src` directory
2. Scheduled using tools like Airflow, Google Cloud Composer, or cron jobs
3. Monitored with custom alerts or existing observability platforms

## Database Schema

### PostgreSQL Tables

#### crypto_prices
```sql
CREATE TABLE crypto_prices (
    id SERIAL PRIMARY KEY,
    timestamp TIMESTAMP NOT NULL,
    trading_pair VARCHAR(20) NOT NULL,
    open NUMERIC(20, 8) NOT NULL,
    high NUMERIC(20, 8) NOT NULL,
    low NUMERIC(20, 8) NOT NULL,
    close NUMERIC(20, 8) NOT NULL,
    volume NUMERIC(30, 8) NOT NULL,
    base_asset VARCHAR(10) NOT NULL,
    quote_asset VARCHAR(10) NOT NULL,
    price_change NUMERIC(20, 8),
    price_change_pct NUMERIC(10, 4),
    volatility NUMERIC(20, 8),
    date DATE,
    hour INTEGER
);

CREATE INDEX idx_crypto_prices_pair_time ON crypto_prices(trading_pair, timestamp);
CREATE INDEX idx_crypto_prices_date ON crypto_prices(date);
```

## API Rate Limits

Be mindful of rate limits when working with cryptocurrency APIs:

- **Coinbase**: 10 requests per second for market data

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add some amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## License

This project is licensed under the MIT License - see the LICENSE file for details.

## Acknowledgments

- Coinbase API for market data
- The cryptocurrency community for documentation and examples
