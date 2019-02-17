# Kubernetes-traning
### Namespace
* export NAMESPACE=<your_namespace>
* kubectl get namespaces
* kubectl create namespace $NAMESPACE
* kubectl config set-context kube-foundation --namespace=$NAMESPACE

### Deployment
* kubectl create deployment nginx --image=nginx
* kubectl get deploy
* kubectl get rs
* kubectl get pods -o wide
* kubectl get pods --show-labels
* kubectl get deploy -o json
* kubectl get deploy -o yaml
* kubectl delete deploy nginx
#### From template
* kubectl apply -f deployment/nginx_deployment.yaml --record
* kubectl get deploy
* kubectl rollout status deployment nginx
* kubectl get rs
* kubectl get pods -o wide --show-labels=true
* kubectl describe deploy/nginx
#### Labels
* kubectl label pod … app-
* kubectl get pods -o wide --show-labels=true
* kubectl label pod ... app=nginx

### Service
#### Deploy busybox in default namespace
* kubectl -n default create deploy busybox --image=yauritux/busybox-curl -- /bin/sh -c "while true; do sleep 30; done;"
#### Check DNS name
* kubectl -n default exec -it $(kubectl -n default get pods | grep busybox | awk '{print $1}') sh
* curl nginx.cdp
* exit
#### Deploy service
* cat services/nginx_service.yaml
* kubectl apply -f services/nginx_service.yaml
* kubectl get services
* kubectl get ep
* kubectl -n default exec -it $(kubectl -n default get pods | grep busybox | awk '{print $1}') sh
* curl nginx.cdp
#### Headless service
* cat services/nginx_headless_service.yaml
* cat services/nginx_headless_service.yaml | kubectl replace --force -f -
* kubectl -n default exec -it $(kubectl -n default get pods | grep busybox | awk '{print $1}') sh
* curl nginx.cdp
* nslookup nginx.cdp
* cat services/nginx_service.yaml | kubectl replace --force -f -
#### External service
* cat external-service/external_service.yaml
* kubectl apply -f external-service/external_service.yaml
* kubectl -n default exec -it $(kubectl -n default get pods | grep busybox | awk '{print $1}') sh
* curl service.cdp
* nslookup service.cdp
* exit 
* cat external-service/external_ep.yaml
* kubectl apply -f external-service/external_ep.yaml
* kubectl -n default exec -it $(kubectl -n default get pods | grep busybox | awk '{print $1}') sh
* curl service.cdp
* exit
#### NodePort
* kubectl patch svc nginx -p '{"spec": {"type":"NodePort"}}'
* kubectl get svc
#### Ingress
* cat ingress/nginx-ingress.yaml
* kubectl apply -f ingress/nginx-ingress.yaml

### Applications
#### Dry-run
* kubectl create deployment nginx1 --image=nginx --dry-run=true
* kubectl create deployment nginx1 --image=nginx --dry-run=true –o yaml > nginx.yaml
#### Environments
* cat env-variables/nginx_deployment_env.yaml
* kubectl apply -f env-variables/nginx_deployment_env.yaml
* kubectl describe deploy nginx
* kubectl exec -it $(kubectl get pods | grep nginx | awk '{print $1}' | head -1) bash
* echo $DEMO_KUBERNETES
#### Envs from
* cat env-from/nginx_deployment_envFrom.yaml
* kubectl apply -f env-from/nginx_deployment_envFrom.yaml
* kubectl describe deploy nginx
* kubectl exec -it $(kubectl get pods | grep nginx | awk '{print $1}' | head -1) bash
* env | grep NAMESPACE

### Scheduling
#### Node selector
* kubectl get nodes --show-labels
* cat scheduling/nginx_nodeselector.yaml
* kubectl apply -f scheduling/nginx_nodeselector.yaml
* kubectl label node kube-slave2 type=node-selector
* kubectl get pods -o wide
* kubectl delete -f scheduling/nginx_nodeselector.yaml
#### Node affinity
* cat scheduling/nginx_nodeaffinity_preffered.yaml
* kubectl apply -f scheduling/nginx_nodeaffinity_preffered.yaml
* kubectl get pods -o wide
* cat scheduling/nginx_nodeaffinity_required.yaml
* kubectl apply -f scheduling/nginx_nodeaffinity_required.yaml
* kubectl get pods -o wide
* kubectl label node kube-slave1 type=node-affinity
* kubectl get pods -o wide
* kubectl edit deployments nginx-node-affinity(show anti-affinity operator NotIn)
* kubectl get pods -o wide
* kubectl delete -f scheduling/nginx_nodeaffinity_required.yaml
#### Pod affinity
* cat scheduling/redis_podantiaffinity.yaml
* kubectl apply -f scheduling/redis_podantiaffinity.yaml
* kubectl get pods -o wide
* kubectl scale deployment redis-cache --replicas=4
* kubectl get pods -o wide
* kubectl scale deployment redis-cache --replicas=2
* kubectl get pods -o wide
* cat scheduling/webserver_podaffinity.yaml
* kubectl apply -f scheduling/webserver_podaffinity.yaml
* kubectl scale deployment web-server --replicas=4
* kubectl get pods -o wide
* kubectl delete -f scheduling/redis_podantiaffinity.yaml
* kubectl delete -f scheduling/webserver_podaffinity.yaml
#### Taint and tolerations
* kubectl get node kube-master -o jsonpath='{.spec.taints[]}{"\n"} '
* kubectl get node kube-master -o jsonpath='{.spec.taints[?(@.key=="kubernetes.io/role")].value}{"\n"}'
* cat scheduling/redis_toleration.yaml
* kubectl apply -f scheduling/redis_toleration.yaml
* kubectl get pods -o wide
* kubectl scale deployment redis-cache --replicas=4
* kubectl get pods -o wide
* kubectl taint node kube-master cdp=test:NoSchedule
* kubectl get pods -o wide
* kubectl taint node kube-master cdp-
* kubectl taint node kube-master cdp=test:NoExecute
* kubectl get pods -o wide
* kubectl taint node kube-master cdp-
* kubectl delete -f scheduling/redis_toleration.yaml

### Logs
* kubectl logs $(kubectl get pods | grep nginx | awk '{print $1}' | head -1)

### Compute resourses
* cat resouces/nginx_resources.yaml
* kubectl apply -f resouces/nginx_resources.yaml
* kubectl describe pods $(kubectl get pods | grep nginx-resources | awk '{print $1}' | head -n 1)
* kubectl delete -f resouces/nginx_resources.yaml

### Deployment update
* kubectl set image deploy nginx nginx=nginx:1.133 --record=true
* kubectl rollout status deploy nginx
* kubectl get pods
* kubectl rollout history deploy nginx
* kubectl rollout history deploy nginx --revision=2
* kubectl rollout undo deploy nginx
* kubectl rollout undo deploy nginx --to-revision=1

### Kubectl explain
* kubectl explain pods.spec.containers

### Config maps
* kubectl create configmap nginx-config --from-file=config-maps/index.html --from-literal=special.how=very
* cat consume-config-maps/nginx_deployment_env.yaml
* kubectl apply -f consume-config-maps/nginx_deployment_env.yaml
* cat consume-config-maps/nginx_deployment_volume.yaml
* kubectl apply -f consume-config-maps/nginx_deployment_volume.yaml

### Secrets
* kubectl create secret generic db-user-pass --from-file=username=secrets/username.txt --from-file=password=secrets/password.txt
* kubectl get secret db-user-pass -o yaml
* kubectl get secret db-user-pass -o jsonpath='{.data.password}‘
* kubectl get secret db-user-pass -o jsonpath='{.data.password}' | base64 -d










