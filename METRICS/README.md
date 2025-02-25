# Metrics

Monitoring your application and troubleshooting for better performance becomes a major part of the DevOps routine.

➡️ Deployed applications can be monitored with their metrics (quantitative data), logs, or traces (event-triggered routines). Later, troubleshooting and debugging comes under the umbrella term - observability.

➡️ I deployed a ToDo application (clone the git repo for todo notes app from <https://github.com/RahulAggarwal-DevOps/node-todo-cicd>) via docker-compose, and collected it's metrics using cAdvisor. cAdvisor is a tool to collect metrics of all the containerized applications running on the host.

➡️ In parallel, the host metrics, eg: CPU usage, memory usage, etc, are collected using NodeExporter.

➡️ Both cAdvisor and NodeExporter ingest their metric data to a time series database - Prometheus. We can inform prometheus about the targets to ingest data from, using a configuration file - prometheus.yml. 

➡️ Below are my targets in the prometheus.yml file :
~~~
scrape_configs:

 - job_name: "prometheus"
 static_configs:
 - targets: ["localhost:9090"]
 labels:
 app: "prometheus"

 - job_name: "cAdvisor"
 static_configs:
 - targets: ["cadvisor:8080"]
 labels:
 app: "cAdvisor"

 - job_name: "node-exporter"
 static_configs:
 - targets: ["node-exporter:9100"]
 labels:
 app: "node-exporter"
~~~ 

➡️ All the above-noted services are deployed with a single docker-compose file. Just run "docker compose up -d --build".

➡️ Now that the setup is ready, I can add a query in prometheus GUI to display specific metric. Prometheus also provides alerting and visualization for our metrics. 

➡️ But, there is a better tool to let Prometheus data get visualized more interactively - Grafana. Grafana is majorly used for visualization, along with alerting and querying. I deployed Grafana, and added Prometheus IP:port in the data source of Grafana. 
