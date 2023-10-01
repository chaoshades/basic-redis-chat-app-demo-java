# K8s Demo

> Source : <https://github.com/redis-developer/basic-redis-chat-app-demo-java>

## Podman/Docker

1. Compile Java app container

   ```shell
   podman build . -f .\Dockerfile -t chat-app-demo
   docker build . -f .\Dockerfile -t chat-app-demo
   ```

2. Start the app container in a pod mapping the port to be accessible from the host

   ```shell
   podman run -d -p 8080:8080 --pod new:mypod chat-app-demo
   docker run -d -p 8080:8080 -e REDIS_ENDPOINT_URL=host.docker.internal:6379 chat-app-demo
   ```

3. Start Redis too

   ```shell
   podman run -d --pod mypod redis
   docker run -d -p 6379:6379 redis
   ```

4. Open a browser : <http://localhost:8080>

## Kubernetes

1. Deploy k8s resources

   ``` shell
   kubectl apply -R -f .\k8s
   ```

   > Deployment
   >
   > - env var
   > - resources
   > - liveness/readiness
   > Service  
   > PVC  
   > Secrets/ConfigMaps
   >
   > - mount as env
   > - mount as files (auto refresh)
   > HPA  
   > Network Policies

2. Port-forward the service for easier access from localhost

   ```shell
   kubectl port-forward services/chat-app-svc 42775:8080 -n chat-app-demo
   ```

   > You can also instead edit the service type and change it to NodePort instead of ClusterIP.

3. Open a browser : <http://localhost:42775>

You will need to install metric-server if you want to use HPA or see cpu/memory usage of your pods :

> ```shell
> kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/download/v0.6.4/components.yaml
> kubectl -n kube-system patch deployment metrics-server --type=json -p='[{"op": "add", "path": "/spec/template/spec/containers/0/args/-", "value": "--kubelet-insecure-tls"}]]'
> ```
