# docker-compose
Install and run angryquiz using docker-compose

### Delete all docker containers

```
docker stop $(docker ps -a -q)
docker rm $(docker ps -a -q)
```

### Update host file with your ip. Replace angryquizhost with your ip. 

* On Linux/mac it is in /etc/hosts. On Windows, C:\Windows\System32\Drivers\etc\hosts

```
cat /etc/hosts

##
# Host Database
#
# localhost is used to configure the loopback interface
# when the system is booting.  Do not change this entry.
##
127.0.0.1	localhost
192.168.0.17    angryquizhost
255.255.255.255	broadcasthost
::1             localhost 

```

### Run docker compose commands

* git https://github.com/angryquiz/docker-compose.git
* mkdir -p ~/angryquiz-volume-data/elasticsearchdata
* cd docker-compose
* Run the following commands:
* docker-compose up (or below command)
* docker-compose up -d (detached or running in background)
* docker-compose ps (status)
* docker-compose stop (stop)
* docker-compose down --volumes (remove the data volume/imported data)

### Start All


mkdir -p ~/angryquiz-volume-data/elasticsearchdata && mkdir -p ~/angryquiz-docker-data && curl -L https://raw.githubusercontent.com/angryquiz/docker-compose/master/docker-compose.yml > ~/angryquiz-docker-data/docker-compose.yml && cd ~/angryquiz-docker-data && docker-compose up -d



### Status All


mkdir -p ~/angryquiz-docker-data && curl -L https://raw.githubusercontent.com/angryquiz/docker-compose/master/docker-compose.yml > ~/angryquiz-docker-data/docker-compose.yml && cd ~/angryquiz-docker-data && docker-compose ps


### Import sample data

```

mkdir -p ~/angryquiz-sample-data

curl -L https://raw.githubusercontent.com/angryquiz/docs/master/question-bank-es-mapping.json > ~/angryquiz-sample-data/question-bank-es-mapping.json

docker run --net=host --rm -ti -v ~/angryquiz-sample-data:/tmp taskrabbit/elasticsearch-dump \
  --input=/tmp/question-bank-es-mapping.json \
  --output=http://localhost:9200/questionbank \
  --type=mapping

curl -L https://raw.githubusercontent.com/angryquiz/docs/master/question-bank-es-data.json > ~/angryquiz-sample-data/question-bank-es-data.json

docker run --net=host --rm -ti -v ~/angryquiz-sample-data:/tmp taskrabbit/elasticsearch-dump \
  --input=/tmp/question-bank-es-data.json\
  --output=http://localhost:9200/questionbank \
  --type=data
```

### Stop All

* this will cleanup imported data. Will have to re-import

mkdir -p ~/angryquiz-docker-data && curl -L https://raw.githubusercontent.com/angryquiz/docker-compose/master/docker-compose.yml > ~/angryquiz-docker-data/docker-compose.yml && cd ~/angryquiz-docker-data && docker-compose stop && docker ps --filter "status=exited" | awk '{print $1}' | xargs docker rm

### Stop AngryQuiz only (redis and elasticsearch will not be stopped)

docker ps | grep 'angryquiz77/angryquiz:latest' | awk '{print $1}' | xargs docker stop
docker ps | grep 'angryquiz77/angryquiz:latest' | awk '{print $1}' | xargs docker rm

### Start AngryQuiz only 

docker run -d -p 8080:8080 angryquiz77/angryquiz:latest

### Test

* Kibana - http://angryquizhost:5601/app/kibana
* ElasticSearch - http://angryquizhost:9200/
* Question REST API - http://angryquizhost:8080/question-rest/apidocs/
* Bank REST API - http://angryquizhost:8080/question-bank/apidocs/
* Redis - telnet angryquizhost 6379 (Ctrrl-C when done)
* AngryQuiz Dashboard - http://angryquizhost:8080/ 
* AngryQuiz Search - http://angryquizhost:8080/dashboard/#!/questionBankSearch (search for 'a')

### Cleanup (removes sample and temp data)

```
rm -rf ~/angryquiz-*

```

