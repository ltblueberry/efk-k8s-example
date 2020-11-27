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


Deploy elasticsearch release from the helm chart
```
helm install elasticsearch elastic/elasticsearch --set imageTag="7.9.1" -n monitoring
```
or if you want to change the storage size
```
helm install elasticsearch elastic/elasticsearch --set imageTag="7.9.1",volumeClaimTemplate.resources.requests.storage="60Gi" -n monitoring
```

![elastic pods](screenshots/screenshot-elastic-pods.png)


# Kibana

Deploy [Kibana manifests](kibana/)

```
kubectl apply -f kibana/
```

![kibana-deploy](screenshots/screenshot-kibana-deploy.png)


# Example domain

Add our example domain to **/etc/hosts**
```
34.66.45.242  kibana.example.com
```

# Kibana Lifecycle Policy
Create Index Lifecycle Policy to delete stale indices

Go to the side menu and open **Management**/**Dev Tools**


Next request will create **Index Lifecycle Policy**, that will delete indices older that 20 days
```
PUT _ilm/policy/delete-20-days
{
    "policy" : {
      "phases" : {
        "delete" : {
          "min_age" : "20d",
          "actions" : {
            "delete" : { }
          }
        }
      }
    } 
}
```

![kibana-policy](screenshots/screenshot-kibana-policy.png)


Next request will create **Index Template** and bind the created lifecycle policy

```
PUT _template/example_template
{
  "index_patterns": ["example-*"],
  "settings": {
    "index.lifecycle.name": "delete-20-days"
  }
}
```

![kibana-index-template](screenshots/screenshot-kibana-index-template.png)


Now all indices that match the example index template will apply that lifecycle policy when created.