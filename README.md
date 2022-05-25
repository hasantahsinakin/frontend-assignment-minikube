# frontend-assignment-minikube

```
minikube kubectl -- get all

NAME                                                READY   STATUS    RESTARTS   AGE
pod/database-679d77bd89-2pt22                       1/1     Running   0          105m
pod/es-cluster-0                                    1/1     Running   0          103m
pod/filebeat-mjccw                                  1/1     Running   0          100m
pod/grafana-d97958b5f-27dvz                         1/1     Running   0          43m
pod/kibana-dep-7f677b4757-mm9f8                     1/1     Running   0          99m
pod/logstash-dep-65bbd65f76-4chwz                   1/1     Running   0          101m
pod/prometheus-alertmanager-94b6f74cf-xh6q2         2/2     Running   0          50m
pod/prometheus-kube-state-metrics-748fc7f64-w2k89   1/1     Running   0          50m
pod/prometheus-node-exporter-hsh2v                  1/1     Running   0          50m
pod/prometheus-pushgateway-5f5cdd4569-d6dkt         1/1     Running   0          50m
pod/prometheus-server-7f4bb6844b-9mw54              2/2     Running   0          50m
pod/web-6f6d6d97bb-49mnt                            1/1     Running   0          104m

NAME                                    TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)                         AGE
service/database                        ClusterIP   10.96.181.182    <none>        5432/TCP                        104m
service/elasticsearch                   NodePort    10.110.147.188   <none>        9200:32520/TCP,9300:32445/TCP   103m
service/grafana                         ClusterIP   10.101.70.204    <none>        80/TCP                          43m
service/grafana-np                      NodePort    10.96.214.133    <none>        80:30015/TCP                    28m
service/kibana-svc                      NodePort    10.109.145.147   <none>        5601:32061/TCP                  99m
service/kubernetes                      ClusterIP   10.96.0.1        <none>        443/TCP                         106m
service/logstash-svc                    ClusterIP   10.102.249.9     <none>        5044/TCP                        101m
service/prometheus-alertmanager         ClusterIP   10.105.24.131    <none>        80/TCP                          50m
service/prometheus-kube-state-metrics   ClusterIP   10.101.192.104   <none>        8080/TCP                        50m
service/prometheus-node-exporter        ClusterIP   10.106.146.27    <none>        9100/TCP                        50m
service/prometheus-pushgateway          ClusterIP   10.106.125.108   <none>        9091/TCP                        50m
service/prometheus-server               ClusterIP   10.110.49.48     <none>        80/TCP                          50m
service/prometheus-server-np            NodePort    10.105.125.31    <none>        80:32730/TCP                    49m
service/web                             NodePort    10.101.130.203   <none>        3000:32021/TCP                  103m

NAME                                      DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR   AGE
daemonset.apps/filebeat                   1         1         1       1            1           <none>          100m
daemonset.apps/prometheus-node-exporter   1         1         1       1            1           <none>          50m

NAME                                            READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/database                        1/1     1            1           105m
deployment.apps/grafana                         1/1     1            1           43m
deployment.apps/kibana-dep                      1/1     1            1           99m
deployment.apps/logstash-dep                    1/1     1            1           101m
deployment.apps/prometheus-alertmanager         1/1     1            1           50m
deployment.apps/prometheus-kube-state-metrics   1/1     1            1           50m
deployment.apps/prometheus-pushgateway          1/1     1            1           50m
deployment.apps/prometheus-server               1/1     1            1           50m
deployment.apps/web                             1/1     1            1           104m

NAME                                                      DESIRED   CURRENT   READY   AGE
replicaset.apps/database-679d77bd89                       1         1         1       105m
replicaset.apps/grafana-d97958b5f                         1         1         1       43m
replicaset.apps/kibana-dep-7f677b4757                     1         1         1       99m
replicaset.apps/logstash-dep-65bbd65f76                   1         1         1       101m
replicaset.apps/prometheus-alertmanager-94b6f74cf         1         1         1       50m
replicaset.apps/prometheus-kube-state-metrics-748fc7f64   1         1         1       50m
replicaset.apps/prometheus-pushgateway-5f5cdd4569         1         1         1       50m
replicaset.apps/prometheus-server-7f4bb6844b              1         1         1       50m
replicaset.apps/web-6f6d6d97bb                            1         1         1       104m

NAME                          READY   AGE
statefulset.apps/es-cluster   1/1     103m
```

**Kibana auditd - filebeat**

```
kubectl exec -it pod/web-6f6d6d97bb-49mnt --container web -- /bin/bash

curl -L -O https://artifacts.elastic.co/downloads/beats/filebeat/filebeat-7.14.1-amd64.deb
dpkg -i filebeat-7.14.1-amd64.deb
apt update && apt install vim -y
vi /etc/filebeat/filebeat.yml
filebeat modules enable auditd
filebeat setup
service filebeat start
```

**Install And Configure Helm**

```
https://docs.bitnami.com/kubernetes/get-started-kubernetes/

curl https://raw.githubusercontent.com/kubernetes/helm/master/scripts/get-helm-3 > get_helm.sh
chmod 700 get_helm.sh
./get_helm.sh
```

**Install Prometheus**

```
https://blog.marcnuri.com/prometheus-grafana-setup-minikube

helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm install prometheus prometheus-community/prometheus

kubectl expose service prometheus-server --type=NodePort --target-port=9090 --name=prometheus-server-np
service/prometheus-server-np exposed

minikube service prometheus-server-np --url
http://192.168.49.2:32730
```

**Install Grafana**

```
https://github.com/grafana/helm-charts/blob/main/charts/grafana/README.md

helm repo add grafana https://grafana.github.io/helm-charts
helm install grafana grafana/grafana

W0525 16:12:21.481507   45786 warnings.go:70] policy/v1beta1 PodSecurityPolicy is deprecated in v1.21+, unavailable in v1.25+
W0525 16:12:21.482404   45786 warnings.go:70] policy/v1beta1 PodSecurityPolicy is deprecated in v1.21+, unavailable in v1.25+
W0525 16:12:21.514366   45786 warnings.go:70] policy/v1beta1 PodSecurityPolicy is deprecated in v1.21+, unavailable in v1.25+
W0525 16:12:21.516214   45786 warnings.go:70] policy/v1beta1 PodSecurityPolicy is deprecated in v1.21+, unavailable in v1.25+
NAME: grafana
LAST DEPLOYED: Wed May 25 16:12:21 2022
NAMESPACE: default
STATUS: deployed
REVISION: 1
NOTES:
1. Get your 'admin' user password by running:

   kubectl get secret --namespace default grafana -o jsonpath="{.data.admin-password}" | base64 --decode ; echo

2. The Grafana server can be accessed via port 80 on the following DNS name from within your cluster:

   grafana.default.svc.cluster.local

   Get the Grafana URL to visit by running these commands in the same shell:

     export POD_NAME=$(kubectl get pods --namespace default -l "app.kubernetes.io/name=grafana,app.kubernetes.io/instance=grafana" -o jsonpath="{.items[0].metadata.name}")
     kubectl --namespace default port-forward $POD_NAME 3000

3. Login with the password from step 1 and the username: admin
#################################################################################
######   WARNING: Persistence is disabled!!! You will lose your data when   #####
######            the Grafana pod is terminated.                            #####
#################################################################################

kubectl get secret --namespace default grafana -o jsonpath="{.data.admin-password}" | base64 --decode ; echo
8PktodVwIn7KT5CLgOkNaENZE2uENCcUq3oTXcZE

export POD_NAME=$(kubectl get pods --namespace default -l "app.kubernetes.io/name=grafana,app.kubernetes.io/instance=grafana" -o jsonpath="{.items[0].metadata.name}")

kubectl expose service grafana --type=NodePort --target-port=3000 --name=grafana-np
service/grafana-np exposed

minikube service grafana-np --url
http://192.168.49.2:30015
```
