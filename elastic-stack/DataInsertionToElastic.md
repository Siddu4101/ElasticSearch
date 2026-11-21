Data insertion to Elastic

we have different ways to insert the data into elastic

manual and when we have data exports or data in handy refere(../dataloadAndQuery/README.md)
1. manually importing the data using kibana data-import tool
2. using elasticsearch bulk api to insert the data

when data import needs to be automated or streaming data/logs insertion or some intermidiate transformation is needed or formatting etc
we have existing tools like logstash, filebeat, fluentd, etc

Tool	Purpose
Kibana Data Import	Manual data import
Logstash	Data ingestion and transformation pipelines(Input, Filter, Output)
Beats / Filebeat	Lightweight data collection and forwarding
Elasticsearch REST API	Directly index documents
Elastic Agent	Unified data collection and monitoring


