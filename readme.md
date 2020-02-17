# Setup #

Grab necessary tools and resources:

    git clone git@github.com:Strivacity/k8s-workshop.git
    cd k8s-workshop
    export PATH=`pwd`/bin:`pwd`/istio-1.4.4/bin/:${PATH}

Initialize a local kubernetes cluster:

    minikube start --memory=4096 --cpus=2
    kubectl get nodes

# Istio #

Install istio with demo profile, which will setup istio with tools for monitoring:

    istioctl manifest apply --set profile=demo

Check running system with k9s (new tab/window):

    ./bin/k9s

Expose internal IPs with minikube (new tab/window):

    ./bin/minikube tunnel

List the available services:

    kubectl get services -n istio-system

    NAME                     TYPE           CLUSTER-IP       EXTERNAL-IP      PORT(S)                                                                                                                      AGE
    grafana                  ClusterIP      10.99.6.160      <none>           3000/TCP                                                                                                                     47m
    istio-citadel            ClusterIP      10.96.142.237    <none>           8060/TCP,15014/TCP                                                                                                           47m
    istio-egressgateway      ClusterIP      10.111.191.160   <none>           80/TCP,443/TCP,15443/TCP                                                                                                     47m
    istio-galley             ClusterIP      10.108.227.151   <none>           443/TCP,15014/TCP,9901/TCP,15019/TCP                                                                                         47m
    istio-ingressgateway     LoadBalancer   10.107.150.125   10.107.150.125   15020:31683/TCP,80:30132/TCP,443:30024/TCP,15029:32132/TCP,15030:31443/TCP,15031:30538/TCP,15032:30647/TCP,15443:31616/TCP   47m
    istio-pilot              ClusterIP      10.109.166.135   <none>           15010/TCP,15011/TCP,8080/TCP,15014/TCP                                                                                       47m
    istio-policy             ClusterIP      10.105.88.97     <none>           9091/TCP,15004/TCP,15014/TCP                                                                                                 47m
    istio-sidecar-injector   ClusterIP      10.111.84.91     <none>           443/TCP                                                                                                                      47m
    istio-telemetry          ClusterIP      10.96.180.101    <none>           9091/TCP,15004/TCP,15014/TCP,42422/TCP                                                                                       47m
    jaeger-agent             ClusterIP      None             <none>           5775/UDP,6831/UDP,6832/UDP                                                                                                   47m
    jaeger-collector         ClusterIP      10.104.24.120    <none>           14267/TCP,14268/TCP,14250/TCP                                                                                                47m
    jaeger-query             ClusterIP      10.105.174.193   <none>           16686/TCP                                                                                                                    47m
    kiali                    ClusterIP      10.97.195.28     <none>           20001/TCP                                                                                                                    47m
    prometheus               ClusterIP      10.106.224.244   <none>           9090/TCP                                                                                                                     47m
    tracing                  ClusterIP      10.102.128.220   <none>           80/TCP                                                                                                                       47m
    zipkin                   ClusterIP      10.109.171.44    <none>           9411/TCP                                                                                                                     47m

## URLs

Use the *CLUSTER-IP* of the serice

* grafana
  * http://*CLUSTER-IP*:3000
  * dashboards for the istio system
* kiali
  * http://*CLUSTER-IP*:20001
  * admin/admin
  * mesh visualisation tool
* jaeger-query
  * http://*CLUSTER-IP*:16686
  * tracing tool

# Sample Bookinfo application

Enable istio auti inject on default namespace

    kubectl label namespace default istio-injection=enabled

Deploy sample application Pods

    kubectl apply -f ./istio-1.4.4/samples/bookinfo/platform/kube/bookinfo.yaml

Deploy gateway for external access

    kubectl apply -f ./istio-1.4.4/samples/bookinfo/networking/bookinfo-gateway.yaml

Check the site at http://*EXTERNAL-IP*/productpage, 3 types of review should be available.

Deploy routing resources, for version 1

    kubectl apply -f ./istio-1.4.4/samples/bookinfo/networking/destination-rule-all.yaml
    kubectl apply -f ./istio-1.4.4/samples/bookinfo/networking/virtual-service-all-v1.yaml

Apply canary deployment for version 2

    kubectl apply -f  ./istio-1.4.4/samples/bookinfo/networking/virtual-service-reviews-80-20.yaml

# Cleanup

    minikube delete
