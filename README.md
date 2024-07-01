
# create a cluster
k3d cluster create -p "8000:30000@loadbalancer" --agents 2

# install istioctl to use on CLI
-- Move to the Istio package directory. For example, if the package is istio-1.22.2, move it to ´/opt´ folder: sudo mv ~/istio-1.22.2 /opt/
-- in the nano ~/.bashrc Add the export line at the end of the file: export PATH=/opt/istio-1.22.1/bin:$PATH
-- Reload .bashrc to apply changes: source ~/.bashrc
-- To check if istioctl has been added successfully, run: istioctl version

# installing istio at cluster
istioctl install
 - accepet the default profile

# Inject label to istio crate the sidecar proxy
  kubectl label namespace default istio-injection=enabled

# Configurando addons
-- Prometheus
kubectl apply -f https://raw.githubusercontent.com/istio/istio/release-1.22/samples/addons/prometheus.yaml
-- Kiali (If we got some error, see if prometheus finished it instalation)
kubectl apply -f https://raw.githubusercontent.com/istio/istio/release-1.22/samples/addons/kiali.yaml
--jaeger
kubectl apply -f https://raw.githubusercontent.com/istio/istio/release-1.22/samples/addons/jaeger.yaml
-- Grafana 
kubectl apply -f https://raw.githubusercontent.com/istio/istio/release-1.22/samples/addons/grafana.yaml


# Comands
-- Open istio dashborads
istioctl dashboard <kiali, grafana, ...>
-- make request to pods to test services with a local script
while true;do curl http://localhost:8000; echo; sleep 0.5; done;
-- make request to pods to test services with FORTIO
install: kubectl apply -f https://raw.githubusercontent.com/istio/istio/release-1.22/samples/httpbin/sample-client/fortio-deploy.yaml
create envirment variable: export FORTIO_POD=$(kubectl get pods -l app=fortio -o 'jsonpath={.items[0].metadata.name}')
comand to start the calls: kubectl exec "$FORTIO_POD" -c fortio -- fortio load -c 2 -qps 0 -t 200s -loglevel Warning http://nginx-service:8000

