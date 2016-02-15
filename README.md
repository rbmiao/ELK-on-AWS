There are two elasticsearch instances which are put behind an elastic load balancer. Health check is using port: 9200 



Security group: 
    elasticsearch port: 9200
    kibana port       : 5601

Detailed steps are from https://www.youtube.com/watch?v=ge8uHdmtb1M


Amazon Linux AMI 2015.09

Elasticsearch 1.7.2
===================

Commands
--------
sudo su

yum update -y

cd /root

wget https://download.elastic.co/elasticsearch/elasticsearch/elasticsearch-1.7.2.noarch.rpm

yum install elasticsearch-1.7.2.noarch.rpm -y

rm -f elasticsearch-1.7.2.noarch.rpm

cd /usr/share/elasticsearch/

./bin/plugin -install mobz/elasticsearch-head

./bin/plugin -install lukas-vlcek/bigdesk

./bin/plugin install elasticsearch/elasticsearch-cloud-aws/2.7.1

./bin/plugin --install lmenezes/elasticsearch-kopf/1.5.7

cd /etc/elasticsearch

nano elasticsearch.yml

Config
------
cluster.name: awstutorialseries

cloud.aws.access_key: ACCESS_KEY_HERE

cloud.aws.secret_key: SECRET_KEY_HERE

cloud.aws.region: us-east-1

discovery.type: ec2

discovery.ec2.tag.Name: "AWS Tutorial Series - Elasticsearch"

http.cors.enabled: true

http.cors.allow-origin: "*"

Commands
--------
service elasticsearch start 


On browser: http://[elasticLoadBalancer]:9200
On browser: http://[elasticsearchServerIP]:9200


Logstash 1.5.4-1
==============

Commands
--------
sudo su

yum update -y

cd /root

wget https://download.elastic.co/logstash/logstash/packages/centos/logstash-1.5.4-1.noarch.rpm

yum install logstash-1.5.4-1.noarch.rpm -y

rm -f logstash-1.5.4-1.noarch.rpm

nano /etc/logstash/conf.d/logstash.conf

Config
------
input { file { path => "/tmp/logstash.txt" } } output { elasticsearch { host => "ELASTICSEARCH_URL_HERE" protocol => "http" } }

Commands
--------
service logstash start


Install two plugins on logstash server automatically 
These plugins are head and bigdesk plugins. Bigdesk is like technical standpoint and we can see CPU, memory how things are going, making sure the clusters healthy, charted data across of the nodes.

On browser: http://[elasticLoadBalancer]:9200/_plugin/head/
            http://[elasticLoadBalancer]:9200/_plugin/bigdesk/  
                  -> nodes -> 


Kibana 4.1.2  -- installation steps
============

Commands
--------
sudo su

yum update -y

cd /root

wget https://download.elastic.co/kibana/kibana/kibana-4.1.2-linux-x64.tar.gz

tar xzf kibana-4.1.2-linux-x64.tar.gz

rm -f kibana-4.1.2-linux-x64.tar.gz

cd kibana-4.1.2-linux-x64

nano config/kibana.yml 

Config
------
elasticsearch_url: "ELASTICSEARCH_URL_HERE"

Commands
--------
nohup ./bin/kibana &

Navigate In Browser
-------------------
http://KIBANA_URL:5601/

Configure an index pattern: logstach-*
                         Timestamp ->create
             -> discovery -> see data

