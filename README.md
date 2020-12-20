# kibana

## Issue: Create Index patterns forbidden error. 
~~~
If your storage is low kibana auto changes its config to read-only mode. to deal with it, go to your dev tools console and
run below command:

open kibana-->dev tools--> console

PUT .kibana/_settings
{
"index": {
"blocks": {
"read_only_allow_delete": "false"
}
}
}
~~~
## Issue: kibana pod starting problem
~~~
delete fluent-bit pod
kubectl port-forward es-cluster-0 9200:9200 --namespace=logging &
curl -X DELETE "localhost:9200/*"
delete and create kibana pod
create fluent-bit pod
~~~


# elastic and kibana deployment
~~~
1: Create Name Space

kubectl create -f logging.yaml

2: kubectl create -f elasticsearch_svc.yaml

3: kubectl create -f elasticsearch_statefulset.yaml

optional: 
kubectl port-forward es-cluster-0 9200:9200 --namespace=logging
curl http://localhost:9200/_cluster/state?pretty

4: kubectl create -f kibana.yaml

5: access dashboard from localhost:

kubectl port-forward kibana-6c9fb4b5b7-plbg2 5601:5601 --namespace=logging

http://localhost:5601
~~~

# Fuentd setup:
~~~
6: kubectl create -f fluentd.yaml
~~~
## or 

# Fluent-bit Setup:

~~~
kubectl create -f https://raw.githubusercontent.com/fluent/fluent-bit-kubernetes-logging/master/output/elasticsearch/fluent-bit-ds.yaml
or 
kubectl create -f fluent-bit.yaml


## Create Index patterns

logstash-* use wildcard pattern to capture all the log data in our Elasticsearch cluster

select the @timestamp field, and hit Create index pattern.
