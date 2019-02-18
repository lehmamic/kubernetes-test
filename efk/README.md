# EFK Stack on Azure Kubernetes Service

Source: (https://carlos.mendible.com/2018/10/14/deploying-elastic-search-fluentd-kibana-on-aks-with-helm/)

He got the idea from: (https://medium.com/@timfpark/efk-logging-on-kubernetes-on-azure-4c54402459c4)

And created a Helm chart: (https://github.com/cmendible/kubernetes.samples/tree/master/13.efk.helm)

## Install Helm

```bash
brew install kubernetes-helm
```

## Clone the Helm chart repo

```bash
git clone https://github.com/cmendible/kubernetes.samples
```

## Install the Helm chart

```bash
cd 13.efk.helm
helm install --set namespace=logging ./efk-aks
```

## Browse Kibana

```bash
kubectl proxy
```

And open Kibana in the browser (http://127.0.0.1:8001/api/v1/namespaces/logging/services/kibana-logging/proxy/app/kibana)

## Curator

The EFK stack on kubernetes fills up the storage very quickly. To Clean the elastic search indexes a curator cron job can be added to the cluster.

```yaml
kubectl apply -n logging -f curator.yml
```

*** Open question: how to use the kubernetes secrets in combination with the config maps. ***
