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

## One Line Command

### Start

mkdir -p ~/angryquiz-volume-data/elasticsearchdata && curl -L https://raw.githubusercontent.com/angryquiz/docker-compose/master/docker-compose.yml > docker-compose.yml && docker-compose up -d


### Status

curl -L https://raw.githubusercontent.com/angryquiz/docker-compose/master/docker-compose.yml > docker-compose.yml && docker-compose ps

### Stop

curl -L https://raw.githubusercontent.com/angryquiz/docker-compose/master/docker-compose.yml > docker-compose.yml && docker-compose stop && docker ps --filter "status=exited" | awk '{print $1}' | xargs docker rm

## Sample data and testing

### Import sample data

* Source - sample mapping - https://raw.githubusercontent.com/angryquiz/docs/master/question-bank-es-data.json
* Source - sample data - https://raw.githubusercontent.com/angryquiz/docs/master/question-bank-es-mapping.json
```
docker run --net=host --rm -ti -v /Users/aq/workspaces/angryquiz-stuff/docs:/tmp taskrabbit/elasticsearch-dump \
  --input=/tmp/question-bank-es-mapping.json \
  --output=http://localhost:9200/questionbank \
  --type=mapping

docker run --net=host --rm -ti -v /Users/aq/workspaces/angryquiz-stuff/docs:/tmp taskrabbit/elasticsearch-dump \
  --input=/tmp/question-bank-es-data.json \
  --output=http://localhost:9200/questionbank \
  --type=data
```

### Test

* Kibana - http://angryquizhost:5601/app/kibana
* ElasticSearch - http://angryquizhost:9200/
* Question REST API - http://angryquizhost:8080/question-rest/apidocs/
* Bank REST API - http://angryquizhost:8080/question-bank/apidocs/
* Redis - telnet angryquizhost 6379 (Ctrrl-C when done)
* AngryQuiz Dashboard - http://angryquizhost:8080/ 
* AngryQuiz Search - http://angryquizhost:8080/dashboard/#!/questionBankSearch (search for 'a')


