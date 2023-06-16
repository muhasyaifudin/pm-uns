
## Praktisi Mengajar Universitas Sebelas Maret - Kubernetes

## Deploying Laravel in Kubernetes


**deploy the container image with:**
```bash
kubectl run laravel-kubernetes-demo \
  --restart=Never \
  --image=<my-username>/laravel-kubernetes-demo \
  --port=80 \
  --env=APP_KEY=base64:cUPmwHx4LXa4Z25HhzFiWCf7TlQmSqnt98pnuiHmzgY=
  ```


**check that a Pod is successfully created with:**
```bash
kubectl get pods
```


**Exposing the application**
```bash
kubectl expose pods laravel-kubernetes-demo --type=NodePort --port=80
```


**verify that the Service**
```bash
kubectl get services
```

**obtain the URL of the application (service)**
```bash
minikube service --url=true laravel-kubernetes-demo
```

**delete the Pod with:**
```bash
kubectl delete pod laravel-kubernetes-demo
```


## Deployment definition:

**deployment.yaml**
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: laravel-kubernetes-demo
spec:
  selector:
    matchLabels:
      run: laravel-kubernetes-demo
  template:
    metadata:
      labels:
        run: laravel-kubernetes-demo
    spec:
      containers:
        - name: demo
          image: <my-username>/laravel-kubernetes-demo
          ports:
            - containerPort: 80
          env:
            - name: APP_KEY
              value: base64:cUPmwHx4LXa4Z25HhzFiWCf7TlQmSqnt98pnuiHmzgY=
```


**submit the Deployment to the cluster**
```bash
kubectl apply -f deployment.yaml
```



## Scaling the application

**scale the Deploymen**
```bash
kubectl scale --replicas=3 deployment/laravel-kubernetes-demo
```

**verify it with**
```bash
kubectl get deployment,pods
```


## Using Nginx Ingress to expose the app

**ingress.yaml**
```bash
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: laravel-kubernetes-demo-ingress
  annotations:
    ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
    - http:
        paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: laravel-kubernetes-demo
                port:
                  number: 80
```

**enable Ingress controller**

```bash
minikube addons enable ingress
```

**create the Ingress**
```bash
kubectl create -f ingress.yaml
```

**verify and obtain the Ingress' information**
```bash
kubectl describe ing laravel-kubernetes-demo-ingress
```



