Monitoring and Alerting with Prometheus and Grafan

 Reduced downtime by 40% by implementing a robust monitoring solution with Prometheus and Grafana in a Kubernetes and Docker environment. 
 Configured Slack integration to enable real-time alerting, improving response times for critical system issues by over 30%. 
 Improved operational efficiency by automating monitoring workflows, reducing manual intervention, and minimizing errors. 
 Key Tools: Prometheus, Grafana, Docker, Kubernetes, Slack

Installation
EC2

PROMETHEUS SERVER ->
>TSDB-TIME SSERIES DATABASE
>HTTP SERVER
>RETRIVEL
>ALERT

docker pull prom/Prometheus
docker run -itd -p 9090:9090 prom/Prometheus
docker run -itd -p 9100:9100 prom/node-exporter:latest

update Prometheus.yml file present on Prometheus server 
ie present in  /etc/Prometheus

# Alertmanager configuration
alerting:
  alertmanagers:
    - static_configs:
        - targets:
          - alertmanager:9093

rule_files:
  - "alerts-rules.yml"

static_configs:
      - targets: ["localhost:9090"]


  - job_name: "node-exporter1"
    static_configs:
      - targets: ["node-exporter1:9100"]



alertmanager
 docker pull prom/alertmanager
 docker run -itd -p 9093:9093 prom/alertmanager


 1. AlertManger Configuration 
 1. Web-hook —> Update 127.0.0.1 —> 192.168. —> Slack channel




1.prometheus server
docker network create monitoring-network
docker network ls | grep network

docker run -itd --name node-exporter1 --network  monitoring-network -p 9100:9100 prom/node-exporter

docker run -itd --name prometheus --network  monitoring-network -v D:\Prometheus-lab\prometheus\prometheus.yml:/etc/prometheus/prometheus.yml -p 9090:9090 prom/Prometheus

docker restart Prometheus


Create account on slack.io
Create workspace
Create Channel
  -right click-->add app-->Incoming WebHooks -->select your channel-->copy url -->configure in alertmanager.yml


 mkdir alertmanager

create file  alertmanager.yml >

update the following according to your slack account configuration

- api_url: 'https://hooks.slack.com/services/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'
        channel: '# prometheus-monitor'     # Ensure this channel exists in Slack and starts with a '#'


docker run -itd --name alertmanager --network  monitoring-network -v D:\Prometheus-lab\alertmanager\alertmanager.yml:\etc\alertmanager\alertmanager.yml -p 9093:9093 prom/alertmanager   

--> in prometheus.yml change configuration
 
# Alertmanager configuration
alerting:
  alertmanagers:
    - static_configs:
        - targets:
          - alertmanager:9093

->save

docker restart prometheus 



--> add alert-rules.yml file in Prometheus directory
--> update rule in  Prometheus.yml file 

rule_files:
  - "alerts-rules.yml"
  # - "second_rules.yml"

--> docker run -itd --name prometheus --network  monitoring-network -v D:\Prometheus-lab\prometheus:/etc/prometheus -p 9090:9090 prom/prometheus  



docker stop node-exporter1

-------------------------------------------------------------------------------------

helm install prometheus prometheus-community/prometheus --namespace monitoring --create-namespace
kubectl get pods --namespace monitoring
kubectl get svc --namespace monitoring
 kubectl expose service prometheus-server --namespace monitoring --type=NodePort --target-port=9090 --name=prometheus-server-ext
minikube service prometheus-server-ext -n monitoring
https://helm.sh/docs/intro/install

------------------------------------------------------------------------------------------


Grafana


docker pull grafana/grafana

docker run -itd --name grafana --network  monitoring-network -p 3000:3000 grafana/grafana

 
Grafana from dashboard http://localhost:3000

For password
 docker logs <continer-id-of-grafana> | grep login

search for password
i.e user=admin
    password=admin

>Add new datasource
>search for Prometheus
>Add Prometheus
>connection:> http://prometheus:9090
>save and test

>Add Dashboard -> import dashboard -> search for dashboard e.g. node exporter ->dashboard id copy -> import 
 ->add datasource ->prometheus




