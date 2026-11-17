Learning ElasticSearch

Installation:
For local test i will use below command to install elasticsearch and kibana in my local machine as a docker container.

1. Install: This downaloads a folder with docker-compose and start,stop and uninstall script and downloads the docker image and start them
```bash
curl -fsSL https://elastic.co/start-local | sh
```

2. to start or stop or uninstall elasticsearch and kibana use below commands
```bash
cd elastic-stack-local
./start.sh
./stop.sh
./uninstall.sh
```
