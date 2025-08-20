# AUTOMATED DATA PIPELINE FOR BLUENILE'S DIAMOND ASSORTMENT

## **I. General overview of the project**
---------
### **1. Project description and structure** : 

This project aims at retrieving all diamond data from an online jewellery retailer called [Bluenile](https://www.bluenile.com/).
- Raw data is scraped via playwright/Oxylabs and stored in a GCS bucket;
- Processing and ingestion is done via Cloud Run functions;
- Processed data is ingested in Big Query and modeling is done via dataform



```text
bluenile/
│── cloud_function_1/         # processes raw data to ndjson, ready to be ingested into Big Query
│── cloud_function_2/         # ingests list data
│── cloud_function_3/         # ingest details data
│── dags/                     # contains airflow dags
│── data/                     # contains sample raw data
│── devtools/                 # deprecated, will be deleted
│── functions/ 
│     │── airflow_tasks.py    # contains the code for airflow tasks
│     │── introspection.py    # contains the API requests for Bluenile's database schema 
│     │── scraper.py          # contains the code for the scraper
│     │── utilities.py        # contains utility functions used by the scraper class and airflow tasks
│── scripts/
│     │── airflow_webserver_entrypoint.sh
│── tests/                    # contains unit tests
│── vm_deploy_v1/             # contains deployment scripts to provision resources 
│── Dockerfile                # base image for the airflow scheduler
│── docker_compose_older_airflow.yml  # docker compose file running a version of airflow < 3.0
│── logging.conf              # configuration for logs
│── pyproject.toml            # list of dependencies for poetry 
└── README.md
```

<br><br><br>

### **2. Scraping** : 

- The scraper runs on a single-node airflow instance
- HTTP requests go through a [residential proxy service](https://oxylabs.io/) and query Bluenile's API endpoints at regular intervals
- Raw data is stored in a GCS bucket
  
<img width="805" height="478" alt="image" src="https://github.com/user-attachments/assets/59c8f2e5-4911-43b1-8fe3-e38544e3ec5b" />

<br><br><br>

### **3. Processing and ingesting** : 
- Transformation --> Raw files that land in the bucket trigger a Cloud Run function that parses, transforms and add metadata before storing them back in the bucket
- Batch ingestion --> A second cloud run function, orchestrated with a scheduler job, scans the bucket every 4 hours and merges diamond records into the source tables in Big Query  

<img width="809" height="476" alt="image" src="https://github.com/user-attachments/assets/b029023c-1fc0-4bd0-874b-4cf82e7ceb7a" />

<br><br><br>

### **4. Modelling** : 
- Once in Big Query, the modeling is fairly simple as the size of the dataset is limited. All tables are build using dataform, along with custom assertions to check for data consistency
- SCD table is built for changing prices and availability
- Final cleaned tables display all data on the current Bluenile Assortment, as well as estimated historical sales of diamonds

<img width="799" height="430" alt="image" src="https://github.com/user-attachments/assets/489254d0-1cc3-4916-8b28-fc904022fade" />

<br><br><br>

### **5. Visualizing** : 
- A simple interactive dashboard built in Looker Studio displays aggregated data on the assortment and weekly sales

   <img width="1206" height="632" alt="image" src="https://github.com/user-attachments/assets/135be16d-884e-4d98-ab3f-f2d64edc1049" />
   <br>
   <img width="1201" height="368" alt="image" src="https://github.com/user-attachments/assets/394092c3-03ef-4be6-8fe0-58741ed310df" />
   <br>
   <img width="1190" height="818" alt="image" src="https://github.com/user-attachments/assets/8e69507b-45ed-4695-a219-53f423cc59af" />

<br><br><br>
## **II. Technical stack and dependencies**
---------

<ins>Technical stack<ins>:
- **Language:** Python 3.12
- **Database:** PostgreSQL for Airflow metadata and BigQuery for project data
- **Scraping:** Playwright, Oxylabs
- **Datalake:** Google Cloud Storage
- **ETL/ELT:** Google Cloud run functions, dataform 
- **Infrastructure:** Docker, Docker Compose
- **Visualization:** Looker studio

<ins>Project dependencies<ins>:
requires-python = ">=3.12,<3.13"
dependencies = [
    "playwright (>=1.53.0,<2.0.0)",
    "python-dotenv (>=1.1.1,<2.0.0)",
    "google-cloud-storage (>=3.2.0,<4.0.0)",
    "pytest (>=8.4.1,<9.0.0)",
    "apache-airflow (>=2.10.4, <3.0.0)",
    "apache-airflow-providers-postgres (>=6.2.1,<7.0.0)",
    "google-cloud-bigquery (>=3.35.1,<4.0.0)",
    "pytz (>=2025.2,<2026.0)"
]





 
