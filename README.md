## Deploy Online Boutique Microservice in EKS
<br>
<br>
<br>

<p align="center">
<img src="./img/Hipster_HeroLogoCyan.svg" width="300" alt="Online Boutique" />
</p>

<br>
<br>


## Architecture


[![Architecture of
microservices](./img/architecture-diagram.png)](./img/architecture-diagram.png)
<br>
<br>


### Deploy Prometheus Operator Stack
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
kubectl create namespace monitoring
helm install monitoring prometheus-community/kube-prometheus-stack -n monitoring
helm ls

[Link to the chart: https://github.com/prometheus-community/helm-charts/tree/main/charts/kube-prometheus-stack]


### Check Prometheus Stack Pods
kubectl 
kubectl get all -n monitoring

### Access Prometheus UI
kubectl port-forward svc/monitoring-kube-prometheus-prometheus 9090:9090 -n monitoring &

### Access Grafana
kubectl port-forward svc/monitoring-grafana 8080:80 -n monitoring &
user: admin
pwd: prom-operator

### Trigger CPU spike with many requests

##### Deploy a busybox pod so we can curl our application 
kubectl run curl-test --image=radial/busyboxplus:curl -i --tty --rm

##### create a script which curls the application endpoint. The endpoint is the external loadbalancer service endpoint
for i in $(seq 1 10000)
do
  curl ae4aee0715edc46b988c6ce67121bf57-1459479566.eu-west-3.elb.amazonaws.com > test.txt
done


### Access Alert manager UI
kubectl port-forward -n monitoring svc/monitoring-kube-prometheus-alertmanager 9093:9093 &

#### Create cpu stress
kubectl delete pod cpu-test; kubectl run cpu-test --image=containerstack/cpustress -- --cpu 4 --timeout 60s --metrics-brief


### Deploy Redis Exporter
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo add stable https://charts.helm.sh/stable
helm repo update

helm install redis-exporter prometheus-community/prometheus-redis-exporter -f redis-values.yaml
