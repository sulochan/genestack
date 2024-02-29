Gateway API is L4 and L7 layer routing project in Kubernetes. It represents next generation of k8s Ingress, LB and Service Mesh APIs. For more information on the project see: [Gateway API SIG.](https://gateway-api.sigs.k8s.io/)

**Move from Ingress to Gateway APIs**
Since Gateway APIs are successor to Ingress Controllers there needs to be a one time migration from Ingress -> GW API resources. To learn more about it refer to: [Ingress Migration](https://gateway-api.sigs.k8s.io/guides/migrating-from-ingress/#migrating-from-ingress)


**Resource Models in Gateway API**
There are 3 main resource models in gateway apis:
1. GatewayClass - Mostly managed by a controller.
2. Gateway - An instance of traffic handling infra like a LB.
3. Routes - Defines HTTP-specific rules for mapping traffic from a Gateway listener to a representation of backend network endpoints.

**k8s Gateway API is NOT the same as API Gateways**
While both sound the same, API Gateway is a more of a general concept that defines a set of resources that exposes capabilities of a backend service but also provide other functionalities like traffic management, rate limiting, authentication and more. It is geared towards commercial API management and monetisation. 

From the gateway api sig:
> [!NOTE]
> Most Gateway API implementations are API Gateways to some extent, but not all API Gateways are Gateway API implementations.

**Controller: NGINX Gateway Fabric**
[NGINX Gateway Fabric](https://github.com/nginxinc/nginx-gateway-fabric) is an open-source project that provides an implementation of the Gateway API using nginx as the data plane.

Chart Install: https://github.com/nginxinc/nginx-gateway-fabric/blob/main/deploy/helm-chart/values.yaml

First Install the Gateway API Resource from Kubernetes
```
kubectl apply -f https://github.com/kubernetes-sigs/gateway-api/releases/download/v1.0.0/standard-install.yaml
```

Next, Install the NGINX Gateway Fabric controller
```
cd /opt/genestack/kustomize/nginx-gateway

kubectl kustomize --enable-helm . | kubectl create -n nginx-gateway -f -
```
==NOTE: the install will create a service type of NodePort for the gateway.==

Wait for the deployment to be ready
```
kubectl wait --timeout=5m -n nginx-gateway deployment/ngf-nginx-gateway-fabric --for=condition=Available
```

Helm install does not automatically upgrade the crds for this resource. To upgrade the crds you will have to manually install them. Follow the process from :  [Upgrade CRDs](https://docs.nginx.com/nginx-gateway-fabric/installation/installing-ngf/helm/#upgrade-nginx-gateway-fabric-crds)

**Example Implementation with Prometheus UI**

