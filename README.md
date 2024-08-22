# Flask API Project

## Overview
This project is a Flask API that connects to a PostgreSQL database and exposes endpoints for data retrieval.

## Setup
### Clone the repository:
```bash
git clone https://github.com/muyiwao/APIPython.git
cd APIPython
```

## Project Structure:
```bash
   APIPython/
   │
   ├── src/
   │   ├── __init__.py
   │   ├── dbapi.py                 # This contains your Flask API script
   │   ├── pythonPostgress.py
   │
   ├── k8s/
   │   ├── deployment.yaml         # Kubernetes deployment file
   │   └── service.yaml            # Kubernetes service file
   │
   ├── .env                        # Environment variables file (for local testing)
   ├── .gitignore                  # Git ignore file
   ├── Dockerfile                  # Dockerfile to build the Flask API container
   ├── Jenkinsfile                 # Jenkins pipeline configuration file
   ├── README.md                   # Project README file
   ├── requirements.txt            # Python dependencies
   └── data/
    ├── customers.csv           # Sample data file for your application
    └── data.json   
```

## Documentation:
[PPT - Automated Flask API: Data Retrieval and API Deployment](https://docs.google.com/presentation/d/1POA9AAxhL9brIXMqqRh0sMiqjhSvTKNCBguFZPrcpek/edit#slide=id.p)



