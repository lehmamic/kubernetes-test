# Kubernetes tests

## How To Setup EFK Stack to AKS

### Elasticsearch

Follow the tutorial on this site to install fluentd on the cluster:[https://blogs.msdn.microsoft.com/atverma/2018/09/24/azure-kubernetes-service-aks-deploying-elasticsearch-logstash-and-kibana-elk-and-consume-messages-from-azure-event-hub/](https://blogs.msdn.microsoft-com/atverma/2018/09/24/azure-kubernetes-service-aks-deploying-elasticsearch-logstash-and-kibana-elk-and-consume-messages-from-azure-event-hub/)

Execute flowing command:

```yaml
kubectl apply -n logging -f elasticsearch.yml
```

By default Elasticsearch will be deployed with basic license. After Elasticsearch is deployed, the next step is to activate trail license of Elasticsearch to use x-pack features of Elasticsearch.

Execute following command:

```yml
kubectl port-forward elasticsearch-0 9200:9200 -n logging
```

Now you can access elasticsearch at [http://localhost:9200](http://localhost:9200).

Send a post request to following endpoint with any rest client:

``` bash
POST http://localhost:9200/_xpack/license/start_trial?acknowledge=true
```

With following get request you can check the license:

```bash
GET http://localhost:9200/_xpack/license
```

x-pack security feature of Elasticsearch is used to secure access thus we now need to setup passwords for built-in user accounts and the steps are

Connect to Elasticsearch POD by running command:

```bash
kubectl exec -ti sample-elasticsearch-0 bash
```

Run flowing command to setup built-in user passwords interactively:

```bash
bin/elasticsearch-setup-passwords interactive
```

*** Open question: how to use the kubernetes secrets in combination with the config maps. ***

### Fluentd

Follow the tutorial on this site to install fluentd on the cluster:
[https://blog.ptrk.io/how-to-deploy-an-efk-stack-to-kubernetes/](https://blog.ptrk.io/how-to-deploy-an-efk-stack-to-kubernetes/)

The user rights must be set to enable fluentd to read the logs on azure:

```yaml
env:
    - name: FLUENT_UID
        value: "0"
```

Execute flowing command:

```yaml
kubectl apply -n logging -f fluentd.yml
```

*** Open: exchange hardcoded password with the kubernetes secrets ***

This should basically work as following:

```yaml
env:
    - name: "ELASTICSEARCH_PASSWORD"
        valueFrom:
            secretKeyRef:
                name: efk-password
                key: elastic-password
```

### Kibana

Execute flowing command:

```yaml
kubectl apply -n logging -f kibana.yml
```

*** Open question: how to use the kubernetes secrets in combination with the config maps. ***

### Curator

The EFK stack on kubernetes fills up the storage very quickly. To Clean the elastic search indexes a curator cron job can be added to the cluster.

```yaml
kubectl apply -n logging -f curator.yml
```

*** Open question: how to use the kubernetes secrets in combination with the config maps. ***
