<p align="center">
  <img height="100" src="https://raw.githubusercontent.com/pelias/design/master/logo/pelias_github/Github_markdown_hero.png">
</p>
<h3 align="center">A modular, open-source search engine for our world.</h3>
<p align="center">Pelias is a geocoder powered completely by open data, available freely to everyone.</p>
<p align="center">
<a href="https://github.com/pelias/transit/actions"><img src="https://github.com/pelias/transit/workflows/Continuous%20Integration/badge.svg" /></a>
<a href="https://en.wikipedia.org/wiki/MIT_License"><img src="https://img.shields.io/github/license/pelias/transit?style=flat&color=orange" /></a>
<a href="https://hub.docker.com/u/pelias"><img src="https://img.shields.io/docker/pulls/pelias/transit?style=flat&color=informational" /></a>
<a href="https://gitter.im/pelias/pelias"><img src="https://img.shields.io/gitter/room/pelias/pelias?style=flat&color=yellow" /></a>
</p>
<p align="center">
	<a href="https://github.com/pelias/docker">Local Installation</a> ·
        <a href="https://geocode.earth">Cloud Webservice</a> ·
	<a href="https://github.com/pelias/documentation">Documentation</a> ·
	<a href="https://gitter.im/pelias/pelias">Community Chat</a>
</p>
<details open>
<summary>What is Pelias?</summary>
<br />
Pelias is a search engine for places worldwide, powered by open data. It turns addresses and place names into geographic coordinates, and turns geographic coordinates into places and addresses. With Pelias, you're able to turn your users' place searches into actionable geodata and transform your geodata into real places.
<br /><br />
We think open data, open source, and open strategy win over proprietary solutions at any part of the stack and we want to ensure the services we offer are in line with that vision. We believe that an open geocoder improves over the long-term only if the community can incorporate truly representative local knowledge.
</details>

**Note**: this repository is currently in the process of being migrated from the OpenTransitTools organization in to the Pelias org  During this time some of the documentation and workflows may be changed in order to bring it more in common with other repositories under the pelias org.

# pelias.transit.loader
Load transit landmarks, stops and street intersections into the Pelias geocoder.

#### Setup empty ES index via pelias/schema project
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

##### note: you might need to set an env var to find pelias.json (if you keep getting 'transit' not in your schema errors, try the following):
export PELIAS_CONFIG=${PWD#/cygdrive/c}/pelias.json

 -or-

$Env:PELIAS_CONFIG="$(pwd)\pelias.json"


### to delete transit data from the index:
1. TBD ... each version of Elastic Search has a different way to bulk delete
1. TBD ... so waiting on Pelias to officially use ElasticSearch v5.x  
1. curl -XGET 'http://localhost:9200/pelias/_search?q=source:transit&pretty'
1. might need delete api plugin: https://github.com/pelias/dockerfiles/blob/master/elasticsearch/2.4/Dockerfile


### Docker instructions

######INITIAL CHECKOUT

1. git clone https://github.com/OpenTransitTools/pelias.transit.loader.git
1. cd pelias.transit.loader
1. git update-index --no-assume-unchanged pelias.json
1. git update-index --assume-unchanged pelias.json

######DOWNLOAD DATA

1. export DATA_DIR=/data
1. rm -rf $DATA_DIR/transit/*
1. docker rmi -f pelias_transit
1. mkdir -p $DATA_DIR/transit
1. docker build --tag pelias_transit .
1. docker images
1. docker run -i -v $DATA_DIR:/data -t pelias_transit npm run download
1. ls /data/transit
1. note ... more Pelias / Transit Docker fun available from [OTT Pelias Dockerfiles](https://github.com/OpenTransitTools/pelias.dockerfiles)
