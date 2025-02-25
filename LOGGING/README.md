# Logging 

➡️ Logging is a part of monitoring, where the application logs are captured and visualized for troubleshooting. Just like cAdvisor collects container metrics, Promtail can be used to capture logs in labeled format. We can have multiple services configured with Promtail, and logs from each service will be labeled for distinction. For instance, I have Nginx service running on port 80, and it's logs are available at /var/log/nginx/access.log file. Below is how I configured promtail-config.yaml to capture logs from nginx:
~~~
- job_name: Nginx
 static_configs:
 - targets:
 - localhost
 labels:
 job: NginxLogs
 __path__: /var/log/nginx/*.log
~~~

➡️ Next, just like Prometheus collects metrics from cAdvisor for storage and querying, we can use Loki to collect and store logs from Promtail, and make them queryable. Labeling logs make it much easier to filter data.

➡️ And at last, Loki can be added by Grafana as data source and logs can be visualized. Below is how I configured Promtail, Loki and Grafana in a docker-compose.yaml :
~~~
 loki:
 container_name: loki
 image: grafana/loki:latest
 ports:
 - 3100:3100
 volumes:
 - ./loki-config.yaml:/etc/loki/loki-config.yaml
 depends_on:
 - promtail
 networks:
 - monitoring

 promtail:
 container_name: promtail
 image: grafana/promtail:latest
 volumes:
 - ./promtail-config.yaml:/etc/promtail/promtail-config.yaml
 - /var/log/:/var/log/
 command: --config.file=/etc/promtail/promtail-config.yaml
 networks:
 - monitoring

 grafana:
 image: grafana/grafana-enterprise
 container_name: grafana
 restart: unless-stopped
 ports:
 - 3000:3000
 networks:
 - monitoring
~~~

➡️ Next, Loki can be added as data source on Grafana dashboard. Import a dashboard with promtail and loki, add metrics as job and target as NginxLogs. Logs will be visible. I browsed Nginx with an unidentified postfix and it can be seen in the access logs of Nginx on Grafana dashboard.

➡️ Steps to run:
Since the docker-compose in this folder also has metric based services, clone the git repo for todo notes app from <https://github.com/RahulAggarwal-DevOps/node-todo-cicd>, and then run "docker compose up -d".
