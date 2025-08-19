## AUTOMATED DATA PIPELINE FOR BLUENILE'S DIAMOND ASSORTMENT

**Project description** : 

This project aims at retrieving all diamond data from an online jewellery retailer called [Bluenile](https://www.bluenile.com/).
- Raw data is scraped via playwright/Oxylabs and stored in a GCS bucket;
- Processing and ingestion is done via Cloud Run functions;
- Processed data is ingested in Big Query and modeling is done via dataform



**Project structure** : 
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
