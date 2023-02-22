>This repository is part of the [Pelias](https://github.com/pelias/pelias)
>project. Pelias is an open-source, open-data geocoder originally sponsored by
>[Mapzen](https://www.mapzen.com/). Our official user documentation is
>[here](https://github.com/pelias/documentation).

# transit
Load transit landmarks, stops and street intersections into the Pelias geocoder.

### Setup empty ES index via pelias/schema project
```javascript
cd /srv/pelias_loader/projects/schema
curl -XDELETE 'localhost:9200/pelias?pretty'
node scripts/create_index.js
cd -
```

### to run:
```javascript
npm install
npm start
http://localhost:9200/_cat/indices?v
curl -XGET http://localhost:9200/pelias/_search?pretty=true&q=*:*
curl -XGET http://localhost:3100/v1/search?text=2
curl -XGET http://localhost:9200/pelias/_search?pretty=true&q=name.default:*SMART%20Stop*
```

#### note: you might need to set an env var to find pelias.json (if you keep getting 'transit' not in your schema errors, try the following):
export PELIAS_CONFIG=${PWD#/cygdrive/c}/pelias.json

 -or-

$Env:PELIAS_CONFIG="$(pwd)\pelias.json"


### to delete transit data from the index:
1. TBD ... each version of Elastic Search has a different way to bulk delete
1. TBD ... so waiting on Pelias to officially use ElasticSearch v5.x  
1. curl -XGET 'http://localhost:9200/pelias/_search?q=source:transit&pretty'
1. might need delete api plugin: https://github.com/pelias/dockerfiles/blob/master/elasticsearch/2.4/Dockerfile


### Docker instructions

#### INITIAL CHECKOUT
1. git clone https://github.com/pelias/transit.git
1. cd transit
1. git update-index --no-assume-unchanged pelias.json
1. git update-index --assume-unchanged pelias.json

#### DOWNLOAD DATA
1. export DATA_DIR=./data
1. mkdir -p $DATA_DIR/transit
1. rm -rf $DATA_DIR/transit/*
1. docker rmi -f pelias_transit
1. docker build --tag pelias_transit .
1. docker images
1. docker run -i --network="host" -v $DATA_DIR:/data -t pelias_transit
1. export PELIAS_CONFIG=$PWD/pelias.json
1. bin/download
1. ls /data/transit
1. bin/start  # assumes that you have pelias running on http://127.0.0.1:9200
