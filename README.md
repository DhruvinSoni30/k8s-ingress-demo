### <h1>What is the Ingress?</h1>

The Ingress is a Kubernetes resource that lets you configure an **HTTP load balancer** for applications running on Kubernetes, represented by one or more Services. Such a load balancer is necessary to deliver those applications to clients outside of the Kubernetes cluster.

The Ingress resource supports the following features:


**Host-based routing** 

For example, routing requests with the host header foo.example.com to one group of services and the host header bar.example.com to another group.

**Path-based routing** 

For example, routing requests with the URI that starts with /serviceA to service A and requests with the URI that starts with /serviceB to service B.

Read [Ingress User Guide](https://kubernetes.io/docs/concepts/services-networking/ingress/) to learn more about the Ingress resource.

### <h1>What is the Ingress Controller?</h1>

The Ingress controller is an application that runs in a cluster and configures an HTTP load balancer according to Ingress resources. The load balancer can be a software load balancer running in the cluster or a hardware or cloud load balancer running externally. Different load balancers require different Ingress controller implementations.

In the case of NGINX, the Ingress controller is deployed in a pod along with the load balancer.

### Prerequisites:- 
  
Kubernetes cluster deployed by KOPS
  
# Installing the Ingress Controller In AWS 
  
## 1. Clone this Repository
```
git clone https://github.com/DhruvinSoni30/k8s-ingress-demo.git
```
  
## 2. Run below commands to create services, deployments & pods
```
kubectl apply -f website.yaml
kubectl apply -f spring.yaml
```
  
## 3. Create a Namespace And SA
```
cd deployments
kubectl apply -f common/ns-and-sa.yaml
```
  
## 4. Create RBAC, Default Secret And Config Map

```
 kubectl apply -f common/
```
  
## 5. Deploy the Ingress Controller

We include two options for deploying the Ingress controller:
 * *Deployment*. Use a Deployment if you plan to dynamically change the number of Ingress controller replicas.
 * *DaemonSet*. Use a DaemonSet for deploying the Ingress controller on every node or a subset of nodes.
  
### 5.1 Create a DaemonSet

When you run the Ingress Controller by using a DaemonSet, Kubernetes will create an Ingress controller pod on every node of the cluster.

```
 kubectl apply -f daemon-set/nginx-ingress.yaml
 ```
  
## 6. Check that the Ingress Controller is Running

Check that the Ingress Controller is Running
Run the following command to make sure that the Ingress controller pods are running:
```
kubectl get pods --namespace=nginx-ingress
```
  
## 7. Get Access to the Ingress Controller

 **If you created a daemonset**, ports 80 and 443 of the Ingress controller container are mapped to the same ports of the node where the container is running. To access the Ingress controller, use those ports and an IP address of any node of the cluster where the Ingress controller is running.


### 6.1 Service with the Type LoadBalancer

 Create a service with the type **LoadBalancer**. Kubernetes will allocate and configure a cloud load balancer for load balancing the Ingress controller pods.

**For AWS, run:**
```
$ kubectl apply -f service/loadbalancer-aws-elb.yaml
```

To get the DNS name of the ELB, run:
```
$ kubectl describe svc nginx-ingress --namespace=nginx-ingress
```

`OR`

```
kubectl get svc -n nginx-ingress 
```

You can resolve the DNS name into an IP address using `nslookup`:
```
$ nslookup <dns-name>
```
# 7. Ingress Resource:

### 7.1 Define path based or host based routing rules for your services.
  
If you don't have domain-name then add below entry in the **/etc/hosts** file:
```
<IP-of-DNS> website.dhruvinsoni.com
<IP-of-DNS> springapp.dhruvinsoni.com 
```  
# Run below command to create the Ingress Resource
```
cd ../..
kubectl apply -f ingressresource.yaml
```
  
**Note:-** Above file will create the HOST based routing but if you need to create the PATH based routing use below file.
  
  ### Path Based Routing Example
```	  
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: ingress-resource-1
spec:
  ingressClassName: nginx
  rules:
  - host: <host-name>
    http:
      paths:
      # Default Path(/)
      - backend:
          serviceName: <service-name>
          servicePort: 80
      - path: <different-path>
        backend:
          serviceName: <service-name>
          servicePort: 80	
``` 
