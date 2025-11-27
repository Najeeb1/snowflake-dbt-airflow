# 🚀 dbt + Airflow + Snowflake Data Pipeline

A production-ready data pipeline orchestrating dbt transformations on Snowflake using Apache Airflow and Astronomer Cosmos.

## 📋 Overview

This project demonstrates a modern data engineering stack combining:
- **dbt** for SQL-based data transformations
- **Apache Airflow** for workflow orchestration
- **Snowflake** as the data warehouse
- **Astronomer Cosmos** for seamless dbt-Airflow integration

The pipeline runs dbt models on a daily schedule, transforming raw data into analytics-ready tables in Snowflake.

## 🏗️ Architecture

```
Raw Data (Snowflake) 
    ↓
dbt Models (Transformations)
    ↓
Airflow DAG (Orchestration via Cosmos)
    ↓
Transformed Data (Snowflake)
```

## 📁 Project Structure

```
dbt-dag/
├── dags/
│   ├── dbt_dag.py                    # Main Airflow DAG
│   └── dbt/
│       └── data_pipeline/            # dbt project
│           ├── models/               # dbt models
│           ├── dbt_project.yml       # dbt configuration
│           └── packages.yml          # dbt dependencies
├── requirements.txt                  # Python dependencies
├── Dockerfile                        # Astro Runtime configuration
├── .gitignore
└── README.md
```

## 🔧 Prerequisites

- Python 3.11+
- Docker Desktop
- Astro CLI ([installation guide](https://docs.astronomer.io/astro/cli/install-cli))
- Snowflake account with appropriate permissions

## 🚀 Getting Started

### 1. Clone the Repository

```bash
git clone <your-repo-url>
cd dbt-dag
```

### 2. Install Astro CLI

```bash
# macOS
brew install astro

# Windows
winget install -e --id Astronomer.Astro
```

### 3. Configure Snowflake Connection

Create a `.env` file in the project root (don't commit this!):

```bash
SNOWFLAKE_ACCOUNT=your-account.region
SNOWFLAKE_USER=your-username
SNOWFLAKE_PASSWORD=your-password
SNOWFLAKE_DATABASE=dbt_db
SNOWFLAKE_WAREHOUSE=dbt_wh
SNOWFLAKE_ROLE=dbt_role
SNOWFLAKE_SCHEMA=dbt_schema
```

### 4. Start Airflow

```bash
astro dev start
```

This will:
- Build the Docker containers
- Install all dependencies
- Start Airflow webserver on `http://localhost:8080`
- Start the scheduler and other services

Default credentials: `admin` / `admin`

### 5. Set Up Snowflake Connection in Airflow

1. Navigate to `http://localhost:8080`
2. Go to **Admin → Connections**
3. Click **+** to add a new connection
4. Fill in the details:
   - **Connection Id**: `snowflake_conn`
   - **Connection Type**: `Snowflake`
   - **Account**: `your-account.region` (e.g., `fs81240.eu-central-1`)
   - **Login**: Your Snowflake username
   - **Password**: Your Snowflake password
   - **Schema**: `dbt_schema`
   - **Database**: `dbt_db`
   - **Extra**: 
     ```json
     {
       "warehouse": "dbt_wh",
       "role": "dbt_role"
     }
     ```
5. Click **Test** to verify the connection
6. Click **Save**

### 6. Trigger the DAG

1. In the Airflow UI, find the `dbt_dag`
2. Toggle it **ON** (blue switch)
3. Click the **play button** to trigger a manual run
4. Monitor the execution in the **Grid** or **Graph** view

## 📦 Dependencies

Key Python packages (defined in `requirements.txt`):

```txt
astronomer-cosmos          # dbt-Airflow integration
dbt-core                   # dbt transformation framework
dbt-snowflake             # Snowflake adapter for dbt
apache-airflow-providers-snowflake  # Snowflake provider for Airflow
```

## 🔄 Development Workflow

### Making Changes to dbt Models

1. Edit models in `dags/dbt/data_pipeline/models/`
2. Test locally using dbt CLI (if desired):
   ```bash
   cd dags/dbt/data_pipeline
   dbt run
   ```
3. Commit changes to Git
4. Airflow will automatically pick up the changes

### Modifying the DAG

1. Edit `dags/dbt_dag.py`
2. Save the file
3. Airflow will reload the DAG within 30-60 seconds

### Viewing Logs

```bash
# View scheduler logs
astro dev logs --scheduler

# View webserver logs
astro dev logs --webserver

# View all logs
astro dev logs
```

### Accessing the Airflow Container

```bash
astro dev bash
```

## 🗓️ Schedule

The DAG runs daily (`@daily`) starting from September 10, 2023. Modify the schedule in `dags/dbt_dag.py`:

```python
dbt_snowflake_dag = DbtDag(
    # ...
    schedule="@daily",  # Change to @hourly, @weekly, or cron expression
    # ...
)
```

## 🧪 Testing

### Test Snowflake Connection

```bash
astro dev bash
airflow connections test snowflake_conn
```

### Parse the DAG Manually

```bash
astro dev bash
python /usr/local/airflow/dags/dbt_dag.py
```

### Run dbt Commands Directly

```bash
astro dev bash
cd /usr/local/airflow/dags/dbt/data_pipeline
dbt run --select model_name
```

## 🛠️ Troubleshooting

### DAG Not Showing Up

1. Check for parsing errors:
   ```bash
   astro dev logs --scheduler | grep -i error
   ```
2. Verify file location: DAG must be in `dags/` folder
3. Check Airflow UI: **Admin → Import Errors**

### Connection Failures

1. Verify Snowflake credentials in connection settings
2. Ensure account identifier is correct (format: `account.region`)
3. Check warehouse is running in Snowflake
4. Verify role has appropriate permissions

### Task Failures

1. Click on the failed task in Airflow UI
2. View logs to see the specific error
3. Common issues:
   - SQL syntax errors in dbt models
   - Missing dbt dependencies
   - Snowflake permission issues
   - Network/timeout errors

## 📚 Additional Resources

- [dbt Documentation](https://docs.getdbt.com)
- [Apache Airflow Documentation](https://airflow.apache.org/docs/)
- [Astronomer Cosmos Documentation](https://astronomer.github.io/astronomer-cosmos/)
- [Snowflake Documentation](https://docs.snowflake.com)
- [Astro CLI Documentation](https://docs.astronomer.io/astro/cli/overview)

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch: `git checkout -b feature-name`
3. Commit your changes: `git commit -am 'Add feature'`
4. Push to the branch: `git push origin feature-name`
5. Submit a pull request

## 📝 License

This project is licensed under the MIT License - see the LICENSE file for details.

## 👤 Author

**Najeebullah Hussaini**

## 🙏 Acknowledgments

- Astronomer for the excellent Cosmos library
- The dbt and Airflow communities
- Snowflake for the powerful data warehouse platform

---

**Happy Data Engineering!** 🎉

For questions or issues, please open a GitHub issue or reach out to the maintainers.
