# Example of EFK in K8S cluster


# Example

* the kubernetes cluster with configured persistent volumes and ingress-controller
* **kubectl** ([installation guide is here](https://kubernetes.io/docs/tasks/tools/install-kubectl/)), **helm** ([installation guide is here](https://helm.sh/docs/intro/install/))
* example domain **kibana.example.com**


# Namespace

Create the monitoring namespace
```
kubectl create namespace monitoring
```

![created namespace](screenshots/screenshot-namespace.png)


# Elasticsearch
Install Elasticsearch from [this helm chart](https://github.com/elastic/helm-charts/tree/master/elasticsearch) 

Add and update elastic helm repository
```
helm repo add elastic https://helm.elastic.co
helm repo update
```

![elastic helm repo](screenshots/screenshot-elastic-helm-repo.png)


Deploy elasticsearch release from helm chart
```
helm install elasticsearch elastic/elasticsearch -n monitoring
```
or if you want to change the storage size
```
helm install elasticsearch elastic/elasticsearch --set volumeClaimTemplate.resources.requests.storage="60Gi" -n monitoring
```

![elastic pods](screenshots/screenshot-elastic-pods.png)
