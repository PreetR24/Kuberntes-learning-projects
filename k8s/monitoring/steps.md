helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo list
helm repo update
kubectl apply -f .\namespace.yml
helm install prometheus-stack prometheus-community/kube-prometheus-stack --namespace monitoring --set prometheus.service.nodePort=30000 --set prometheus.service.type=NodePort --set grafana.service.nodePort=31000 --set grafana.service.type=NodePort
kubectl get pods -n monitoring


kubectl port-forward svc/prometheus-stack-grafana 3000:80 -n monitoring
kubectl get secret prometheus-stack-grafana -n monitoring -o jsonpath="{.data.admin-password}"