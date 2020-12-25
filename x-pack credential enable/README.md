# Create security disabled elasticsearch deployment:

## login to any one elasticsearch pod and run the following command :
~~~
elasticsearch-certutil cert -out config/elastic-certificates.p12 -pass ""

collect the following file: /usr/share/elasticsearch/config/elastic-certificates.p12

kubectl -n logging cp es-cluster-0:/usr/share/elasticsearch/config/elastic-certificates.p12 /root
~~~

## Create secret from p12 file: 
~~~
kubectl create secret generic cert-pk12 --from-file=elastic-certificates.p12 --namespace logging


or

cat elastic-certificates.p12 | base64

find the key and create a yaml file like below

apiVersion: v1
kind: Secret
metadata:
  name: cert-p12
  namespace: logging
type: Opaque
data:
  elastic-certificates.p12: |
          MIINbwIBAzCCDS..................


kubectl create -f elasticsearch-security-configmap.yaml
~~~
## Edit deployment to add secret and configmap
~~~
vi elastic-sf.yaml

        volumeMounts:
        - name: elasticsearch-config-vol
          mountPath: /usr/share/elasticsearch/config/elasticsearch.yml
          subPath: elasticsearch.yml

        - name: elasticsearch-cert-vol
          mountPath: /usr/share/elasticsearch/config/elastic-certificates.p12
          subPath: elastic-certificates.p12


      volumes:
      - name: elasticsearch-cert-vol
        secret:
          secretName: cert-p12
      - name: elasticsearch-config-vol
        configMap:
            name: elasticsearch-config

~~~

## Generate Password

~~~
kubectl -n logging exec -it  es-cluster-0 bash
elasticsearch-setup-passwords auto

~~~

## Set credential for kibana
~~~
kubectl create -f kibana-configmap.yaml


vi kibana.yaml
        volumeMounts:
        - name: kibana-conf
          mountPath: /usr/share/kibana/config/kibana.yml
          subPath: kibana.yml

      volumes:
      - name: kibana-conf
        configMap:
          name: kibana-config
~~~
~~~
Login to kibana with elastic superuser --> Stack Management->Users

Add fluentbit user
Add fluentbit role with appropriate indicas and add this role to fluentbit user

~~~
## Update fluent-bit config with credential

[OUTPUT]
    Name  es

    HTTP_User fluentbit
    HTTP_Passwd fluentbit



