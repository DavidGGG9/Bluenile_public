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
```

<br><br><br><br>




1. **Scraping** :

- The scraper runs on a single-node airflow instance
- HTTP requests go through a [residential proxy service](https://oxylabs.io/) and query Bluenile's API endpoints at regular intervals
- Raw data is stored in a GCS bucket
  
<img width="805" height="478" alt="image" src="https://github.com/user-attachments/assets/59c8f2e5-4911-43b1-8fe3-e38544e3ec5b" />




2. **Processing and ingesting** :
- Transformation --> Raw files that land in the bucket trigger a Cloud Run function that parses, transforms and add metadata before storing them back in the bucket
- Batch ingestion --> A second cloud run function, orchestrated with a scheduler job, scans the bucket every 4 hours and merges diamond records into the source tables in Big Query  

<img width="809" height="476" alt="image" src="https://github.com/user-attachments/assets/b029023c-1fc0-4bd0-874b-4cf82e7ceb7a" />



3. **Modeling the data** :
- Once in Big Query, the modeling is fairly simple as the size of the dataset is limited. All tables are build using dataform, along with custom assertions to check for data consistency
- SCD table is built for changing prices and availability
- Final cleaned tables display all data on the current Bluenile Assortment, as well as estimated historical sales of diamonds

<img width="799" height="430" alt="image" src="https://github.com/user-attachments/assets/489254d0-1cc3-4916-8b28-fc904022fade" />

4. **Dashboarding** :
[TBC]

 
