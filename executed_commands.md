kubetcl apply -f sample-pod.yml
kubectl delete -f sample-pod.yml

kubetcl apply -f sample-2pod.yml
kubectl get pods --output wide
kubectl exec -it sample-2pod /bin/bash //executed in default container
kubectl exec -it sample-2pod -c nginx-container /bin/bash
kubectl exec -it sample-2pod -c redis-container /bin/bash
kubectl exec -it sample-2pod -c redis-container ls
kubectl exec -it sample-2pod -c nginx-container -- sh -c "ls -lah"

kubectl apply -f sample-entrypoint.yml
kubectl exec -it sample-entrypoint -- sh -c "apt-get update && apt-get install -y procps && ps aux"

kubectl apply -f sample-externaldns.yml
kubectl exec -it sample-externaldns cat /etc/resolv.conf

kubectl apply -f sample-hostaliases.yml
kubectl exec -it sample-hostaliases cat /etc/hosts

kubectl apply -f sample-rs.yml
kubectl get replicasets -o wide
kubectl get pods -l app=sample-app -o wide

kubectl delete pod sample-rs-pld2g
kubectl get pods -l app=sample-app -o wide
kubectl describe rs sample-rs

sed -e 's|replicas: 3|replicas: 5|' sample-rs.yml | kubectl apply -f -
kubectl get pods -L app
kubectl scale rs sample-rs --replicas 4
kubectl get pods -L app
kubectl get replicasets

kubectl apply -f sample-deplolyment.yml --record
kubectl get replicasets -o yaml | head
kubectl get deployments
kubectl get replicasets
kubectl get pods

kubectl set image deployment sample-deployment nginx-container=nginx:1.13
kubectl rollout status deployment sample-deployment

kubectl rollout history deployment sample-deployment
kubectl rollout history deployment sample-deployment --revision 1
kubectl rollout history deployment sample-deployment --revision 2
kubectl rollout undo deployment sample-deployment --to-revision 1
kubectl get replicasets

katsurai.toshio@mf-0631-mm01:~/tk_work/learn_k8s/k8s_test$ kubectl rollout pause deployment sample-deployment
deployment.apps "sample-deployment" paused
katsurai.toshio@mf-0631-mm01:~/tk_work/learn_k8s/k8s_test$ kubectl set image deployment sample-deployment nginx-container=nginx:1.13
deployment.apps "sample-deployment" image updated
katsurai.toshio@mf-0631-mm01:~/tk_work/learn_k8s/k8s_test$ kubectl rollout status deployment sample-deployment
Waiting for rollout to finish: 0 out of 3 new replicas have been updated...
^C
katsurai.toshio@mf-0631-mm01:~/tk_work/learn_k8s/k8s_test$ kubectl rollout undo deployment sample-deployment
error: you cannot rollback a paused deployment; resume it first with 'kubectl rollout resume deployment/sample-deployment' and try again
katsurai.toshio@mf-0631-mm01:~/tk_work/learn_k8s/k8s_test$ kubectl rollout resume deployment sample-deployment
deployment.apps "sample-deployment" resumed
katsurai.toshio@mf-0631-mm01:~/tk_work/learn_k8s/k8s_test$ kubectl rollout status deployment sample-deployment
Waiting for rollout to finish: 1 old replicas are pending termination...
Waiting for rollout to finish: 1 old replicas are pending termination...
deployment "sample-deployment" successfully rolled out
katsurai.toshio@mf-0631-mm01:~/tk_work/learn_k8s/k8s_test$

kubectl apply -f sample-deployment-recreate.yml
kubectl get replicasets --watch
> the result below reflects the change based on `kubectl set image deployment sample-deployment-recreate nginx-container=nginx:1.13`
NAME                                    DESIRED   CURRENT   READY     AGE
sample-deployment-recreate-86b68b4c5b   3         3         3         46s
sample-deployment-recreate-86b68b4c5b   0         3         3         54s
sample-deployment-recreate-86b68b4c5b   0         3         3         54s
sample-deployment-recreate-86b68b4c5b   0         0         0         54s
sample-deployment-recreate-d5b55f699   3         0         0         0s
sample-deployment-recreate-d5b55f699   3         0         0         0s
sample-deployment-recreate-d5b55f699   3         3         0         0s
sample-deployment-recreate-d5b55f699   3         3         1         2s
sample-deployment-recreate-d5b55f699   3         3         2         2s
sample-deployment-recreate-d5b55f699   3         3         3         2s

kubectl apply -f sample-deployment-rollingupdate.yml
kubectl get replicasets --watch
> the result below reflects the change based on `kubectl set image deployment sample-deployment-rollingupdate nginx-container=nginx:1.13`
> maxUnavailable:0 / maxSurge:1
NAME                                         DESIRED   CURRENT   READY     AGE
sample-deployment-rollingupdate-86b68b4c5b   3         3         3         14s
sample-deployment-rollingupdate-d5b55f699   1         0         0         0s
sample-deployment-rollingupdate-d5b55f699   1         0         0         0s
sample-deployment-rollingupdate-d5b55f699   1         1         0         0s
sample-deployment-rollingupdate-d5b55f699   1         1         1         2s
sample-deployment-rollingupdate-86b68b4c5b   2         3         3         1m
sample-deployment-rollingupdate-d5b55f699   2         1         1         2s
sample-deployment-rollingupdate-86b68b4c5b   2         3         3         1m
sample-deployment-rollingupdate-d5b55f699   2         1         1         2s
sample-deployment-rollingupdate-86b68b4c5b   2         2         2         1m
sample-deployment-rollingupdate-d5b55f699   2         2         1         2s
sample-deployment-rollingupdate-d5b55f699   2         2         2         4s
sample-deployment-rollingupdate-86b68b4c5b   1         2         2         1m
sample-deployment-rollingupdate-86b68b4c5b   1         2         2         1m
sample-deployment-rollingupdate-d5b55f699   3         2         2         4s
sample-deployment-rollingupdate-86b68b4c5b   1         1         1         1m
sample-deployment-rollingupdate-d5b55f699   3         2         2         4s
sample-deployment-rollingupdate-d5b55f699   3         3         2         4s
sample-deployment-rollingupdate-d5b55f699   3         3         3         6s
sample-deployment-rollingupdate-86b68b4c5b   0         1         1         1m
sample-deployment-rollingupdate-86b68b4c5b   0         1         1         1m
sample-deployment-rollingupdate-86b68b4c5b   0         0         0         1m

kubectl apply -f sample-deployment-rollingupdate2.yml
kubectl get replicasets --watch
> the result below reflects the change based on `kubectl set image deployment sample-deployment-rollingupdate nginx-container=nginx:1.13`
> maxUnavailable:1 / maxSurge:0
NAME                                         DESIRED   CURRENT   READY     AGE
sample-deployment-rollingupdate-86b68b4c5b   3         3         3         5s
sample-deployment-rollingupdate-d5b55f699   0         0         0         0s
sample-deployment-rollingupdate-86b68b4c5b   2         3         3         20s
sample-deployment-rollingupdate-d5b55f699   0         0         0         0s
sample-deployment-rollingupdate-d5b55f699   1         0         0         0s
sample-deployment-rollingupdate-86b68b4c5b   2         3         3         20s
sample-deployment-rollingupdate-d5b55f699   1         0         0         0s
sample-deployment-rollingupdate-d5b55f699   1         1         0         0s
sample-deployment-rollingupdate-86b68b4c5b   2         2         2         21s
sample-deployment-rollingupdate-d5b55f699   1         1         1         2s
sample-deployment-rollingupdate-86b68b4c5b   1         2         2         22s
sample-deployment-rollingupdate-86b68b4c5b   1         2         2         22s
sample-deployment-rollingupdate-d5b55f699   2         1         1         2s
sample-deployment-rollingupdate-86b68b4c5b   1         1         1         22s
sample-deployment-rollingupdate-d5b55f699   2         1         1         2s
sample-deployment-rollingupdate-d5b55f699   2         2         1         2s
sample-deployment-rollingupdate-d5b55f699   2         2         2         5s
sample-deployment-rollingupdate-86b68b4c5b   0         1         1         25s
sample-deployment-rollingupdate-86b68b4c5b   0         1         1         25s
sample-deployment-rollingupdate-d5b55f699   3         2         2         5s
sample-deployment-rollingupdate-86b68b4c5b   0         0         0         25s
sample-deployment-rollingupdate-d5b55f699   3         2         2         5s
sample-deployment-rollingupdate-d5b55f699   3         3         2         5s
sample-deployment-rollingupdate-d5b55f699   3         3         3         8s

kubectl apply -f sample-deployment.yml
kubectl scale deployment sample-deployment --replicas=5
sed -e 's|replicas: 3|replicas: 4|' sample-deployment.yml | kubectl apply -f -

kubectl run saple-deployment-cli --image nginx:1.13 --replicas 3 --port 80
kubectl delete deployment saple-deployment-cli

kubectl apply -f sample-statefulset.yml
kubectl get statefulsets
kubectl get persistentvolumeclaims
kubectl get persistentvolumes
kubectl delete -f sample-statefulset.yml
kubectl delete persistentvolumeclaims www-sample-statefulset-{0..4}

kubectl scale statefulset sample-statefulset --replicas 4
sed -e 's|replicas: 3|replicas: 5|' sample-statefulset.yml | kubectl apply -f -


kubectl apply -f sample-statefulset-rollingupdate.yml
kubectl get pods --watch
> the result below reflects the change based on `kubectl set image statefulset sample-statefulset-rollingupdate nginx-container=nginx:1.13`
NAME                                 READY     STATUS    RESTARTS   AGE
sample-statefulset-rollingupdate-0   1/1       Running   0          29s
sample-statefulset-rollingupdate-1   1/1       Running   0          28s
sample-statefulset-rollingupdate-2   1/1       Running   0          26s
sample-statefulset-rollingupdate-3   1/1       Running   0          23s
sample-statefulset-rollingupdate-4   1/1       Running   0          21s
sample-statefulset-rollingupdate-4   1/1       Terminating   0         1m
sample-statefulset-rollingupdate-4   0/1       Terminating   0         1m
sample-statefulset-rollingupdate-4   0/1       Terminating   0         1m
sample-statefulset-rollingupdate-4   0/1       Terminating   0         1m
sample-statefulset-rollingupdate-4   0/1       Pending   0         0s
sample-statefulset-rollingupdate-4   0/1       Pending   0         0s
sample-statefulset-rollingupdate-4   0/1       ContainerCreating   0         0s
sample-statefulset-rollingupdate-4   1/1       Running   0         2s
sample-statefulset-rollingupdate-3   1/1       Terminating   0         1m
sample-statefulset-rollingupdate-3   0/1       Terminating   0         1m
sample-statefulset-rollingupdate-3   0/1       Terminating   0         1m
sample-statefulset-rollingupdate-3   0/1       Terminating   0         1m
sample-statefulset-rollingupdate-3   0/1       Pending   0         0s
sample-statefulset-rollingupdate-3   0/1       Pending   0         0s
sample-statefulset-rollingupdate-3   0/1       ContainerCreating   0         0s
sample-statefulset-rollingupdate-3   1/1       Running   0         2s

katsurai.toshio@mf-0631-mm01:~/tk_work/learn_k8s$ kubectl apply -f sample-job-never-restart.yml
job.batch "sample-job-never-restart" created
katsurai.toshio@mf-0631-mm01:~/tk_work/learn_k8s$ kubectl get pods
NAME                             READY     STATUS    RESTARTS   AGE
sample-job-never-restart-qpc2r   1/1       Running   0          5s
katsurai.toshio@mf-0631-mm01:~/tk_work/learn_k8s$ kubectl exec -it sample-job-never-restart-qpc2r -- sh -c 'kill -9 `pgrep sleep`'
katsurai.toshio@mf-0631-mm01:~/tk_work/learn_k8s$ kubectl get pods
NAME                             READY     STATUS              RESTARTS   AGE
sample-job-never-restart-qpc2r   0/1       Error               0          45s
sample-job-never-restart-xdhrz   0/1       ContainerCreating   0          1s

atsurai.toshio@mf-0631-mm01:~/tk_work/learn_k8s$ kubectl apply -f sample-job-onfailure-restart.yml
job.batch "sample-job-never-onfailure" created
katsurai.toshio@mf-0631-mm01:~/tk_work/learn_k8s$ kubectl get pods
NAME                               READY     STATUS    RESTARTS   AGE
sample-job-never-onfailure-82w94   1/1       Running   0          3s
katsurai.toshio@mf-0631-mm01:~/tk_work/learn_k8s$ kubectl exec -it sample-job-never-onfailure-82w94 -- sh -c 'kill -9 `pgrep sleep`'
katsurai.toshio@mf-0631-mm01:~/tk_work/learn_k8s$ kubectl get pods
NAME                               READY     STATUS    RESTARTS   AGE
sample-job-never-onfailure-82w94   1/1       Running   1          30s
katsurai.toshio@mf-0631-mm01:~/tk_work/learn_k8s$ kubectl exec -it sample-job-never-onfailure-82w94 -- sh -c 'kill -9 `pgrep sleep`'
katsurai.toshio@mf-0631-mm01:~/tk_work/learn_k8s$ kubectl get pods
NAME                               READY     STATUS    RESTARTS   AGE
sample-job-never-onfailure-82w94   0/1       Error     1          34s

kubectl apply -f sample-job-parallel.yml
sed -e 's|parallelism: 3|parallelism: 5|' sample-job-parallel.yml | kubectl apply -f -

katsurai.toshio@mf-0631-mm01:~/tk_work/learn_k8s$ kubectl apply -f sample-cronjob.yml
katsurai.toshio@mf-0631-mm01:~/tk_work/learn_k8s$ kubectl get cronjobs
NAME             SCHEDULE      SUSPEND   ACTIVE    LAST SCHEDULE   AGE
sample-cronjob   */1 * * * *   False     0         <none>          6s
katsurai.toshio@mf-0631-mm01:~/tk_work/learn_k8s$ kubectl get jobs
NAME                        DESIRED   SUCCESSFUL   AGE
sample-cronjob-1558579620   1         0            2s
katsurai.toshio@mf-0631-mm01:~/tk_work/learn_k8s$ kubectl get jobs
NAME                        DESIRED   SUCCESSFUL   AGE
sample-cronjob-1558579620   1         0            1m
sample-cronjob-1558579680   1         0            8s
katsurai.toshio@mf-0631-mm01:~/tk_work/learn_k8s$ kubectl patch cronjob sample-cronjob -p '{"spec":{"suspend":true}}'
cronjob.batch "sample-cronjob" patched
katsurai.toshio@mf-0631-mm01:~/tk_work/learn_k8s$ kubectl get cronjobs
NAME             SCHEDULE      SUSPEND   ACTIVE    LAST SCHEDULE   AGE
sample-cronjob   */1 * * * *   True      2         25s             1m
katsurai.toshio@mf-0631-mm01:~/tk_work/learn_k8s$ kubectl get jobs
NAME                        DESIRED   SUCCESSFUL   AGE
sample-cronjob-1558579620   1         0            4m
sample-cronjob-1558579680   1         0            3m
katsurai.toshio@mf-0631-mm01:~/tk_work/learn_k8s$ kubectl delete -f sample-cronjob.yml

`kubectl run sample-cronjob --schedule "*/1 * * * *" --restart Never --image centos:7 -- sleep 30`
kubectl delete cronjobs sample-cronjob
kubectl delete cronjob sample-cronjob

katsurai.toshio@mf-0631-mm01:~/tk_work/learn_k8s$ kubectl apply -f sample-deployment.yml
deployment.apps "sample-deployment" created
katsurai.toshio@mf-0631-mm01:~/tk_work/learn_k8s$ kubectl apply -f sample-clusterip.yml
service "sample-clusterip" created
katsurai.toshio@mf-0631-mm01:~/tk_work/learn_k8s$ kubectl get pods -o wide
NAME                                 READY     STATUS    RESTARTS   AGE       IP           NODE
sample-deployment-86b68b4c5b-gb922   1/1       Running   0          30s       10.1.0.113   docker-for-desktop
sample-deployment-86b68b4c5b-rhfnn   1/1       Running   0          30s       10.1.0.115   docker-for-desktop
sample-deployment-86b68b4c5b-rw8zn   1/1       Running   0          30s       10.1.0.114   docker-for-desktop
katsurai.toshio@mf-0631-mm01:~/tk_work/learn_k8s$ kubectl get pods sample-deployment-86b68b4c5b-gb922 -o jsonpath='{.metadata.labels}'
map[app:sample-app pod-template-hash:4262460716]
katsurai.toshio@mf-0631-mm01:~/tk_work/learn_k8s$ kubectl get pods -l app=sample-app -o custom-columns="NAME:{metadata.name},IP:{status.podIP}"
NAME                                 IP
sample-deployment-86b68b4c5b-gb922   10.1.0.113
sample-deployment-86b68b4c5b-rhfnn   10.1.0.115
sample-deployment-86b68b4c5b-rw8zn   10.1.0.114
katsurai.toshio@mf-0631-mm01:~/tk_work/learn_k8s$ kubectl get services sample-clusterip
NAME               TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)    AGE
sample-clusterip   ClusterIP   10.111.224.170   <none>        8080/TCP   1m
katsurai.toshio@mf-0631-mm01:~/tk_work/learn_k8s$ kubectl describe svc sample-clusterip
Name:              sample-clusterip
Namespace:         default
Labels:            <none>
Annotations:       kubectl.kubernetes.io/last-applied-configuration={"apiVersion":"v1","kind":"Service","metadata":{"annotations":{},"name":"sample-clusterip","namespace":"default"},"spec":{"ports":[{"name":"http-port",...
Selector:          app=sample-app
Type:              ClusterIP
IP:                10.111.224.170
Port:              http-port  8080/TCP
TargetPort:        80/TCP
Endpoints:         10.1.0.113:80,10.1.0.114:80,10.1.0.115:80
Session Affinity:  None
Events:            <none>


$ for PODNAME in `kubectl get pods -l app=sample-app -o jsonpath='{.items[*].metadata.name}'`; do
    kubectl exec -it ${PODNAME} -- cp /etc/hostname /usr/share/nginx/html/index.html;
done
katsurai.toshio@mf-0631-mm01:~/tk_work/learn_k8s$
katsurai.toshio@mf-0631-mm01:~/tk_work/learn_k8s$ kubectl run --image=centos:6 --restart=Never --rm -i testpod -- curl -s http://10.111.224.170:8080
sample-deployment-86b68b4c5b-gb922
katsurai.toshio@mf-0631-mm01:~/tk_work/learn_k8s$ kubectl run --image=centos:6 --restart=Never --rm -i testpod -- curl -s http://10.111.224.170:8080
sample-deployment-86b68b4c5b-rw8zn
katsurai.toshio@mf-0631-mm01:~/tk_work/learn_k8s$ kubectl run --image=centos:6 --restart=Never --rm -i testpod -- curl -s http://10.111.224.170:8080
sample-deployment-86b68b4c5b-rhfnn
katsurai.toshio@mf-0631-mm01:~/tk_work/learn_k8s$ kubectl run --image=centos:6 --restart=Never --rm -i testpod -- curl -s http://10.111.224.170:8080
sample-deployment-86b68b4c5b-gb922
katsurai.toshio@mf-0631-mm01:~/tk_work/learn_k8s$ kubectl run --image=centos:6 --restart=Never --rm -i testpod -- curl -s http://10.111.224.170:8080
sample-deployment-86b68b4c5b-rw8zn

> if pod is created before service is generated, environment variables are not registerd
katsurai.toshio@mf-0631-mm01:~/tk_work/learn_k8s$ kubectl exec -it sample-deployment-86b68b4c5b-rw8zn env | grep -i sample_clusterip
katsurai.toshio@mf-0631-mm01:~/tk_work/learn_k8s$ kubectl get pods
NAME                                 READY     STATUS    RESTARTS   AGE
sample-deployment-86b68b4c5b-gb922   1/1       Running   0          21m
sample-deployment-86b68b4c5b-rhfnn   1/1       Running   0          21m
sample-deployment-86b68b4c5b-rw8zn   1/1       Running   0          21m
katsurai.toshio@mf-0631-mm01:~/tk_work/learn_k8s$ kubectl delete pod sample-deployment-86b68b4c5b-gb922
pod "sample-deployment-86b68b4c5b-gb922" deleted
katsurai.toshio@mf-0631-mm01:~/tk_work/learn_k8s$ kubectl get pods
NAME                                 READY     STATUS    RESTARTS   AGE
sample-deployment-86b68b4c5b-65vnb   1/1       Running   0          7s
sample-deployment-86b68b4c5b-rhfnn   1/1       Running   0          21m
sample-deployment-86b68b4c5b-rw8zn   1/1       Running   0          21m
katsurai.toshio@mf-0631-mm01:~/tk_work/learn_k8s$ kubectl exec -it sample-deployment-86b68b4c5b-65vnb env | grep -i sample_clusterip
SAMPLE_CLUSTERIP_SERVICE_PORT_HTTP_PORT=8080
SAMPLE_CLUSTERIP_SERVICE_PORT=8080
SAMPLE_CLUSTERIP_SERVICE_HOST=10.111.224.170
SAMPLE_CLUSTERIP_PORT=tcp://10.111.224.170:8080
SAMPLE_CLUSTERIP_PORT_8080_TCP=tcp://10.111.224.170:8080
SAMPLE_CLUSTERIP_PORT_8080_TCP_PROTO=tcp
SAMPLE_CLUSTERIP_PORT_8080_TCP_ADDR=10.111.224.170
SAMPLE_CLUSTERIP_PORT_8080_TCP_PORT=8080

katsurai.toshio@mf-0631-mm01:~/tk_work/learn_k8s$ kubectl run --image=centos:7 --restart=Never --rm -i testpod -- curl -s http://sample-clusterip:8080
sample-deployment-86b68b4c5b-rw8zn

katsurai.toshio@mf-0631-mm01:~/tk_work/learn_k8s$ kubectl run --image=centos:6 --restart=Never --rm -i testpod -- dig sample-clusterip.default.svc.cluster.local
katsurai.toshio@mf-0631-mm01:~/tk_work/learn_k8s$ kubectl run --image=centos:6 --restart=Never --rm -i testpod -- cat /etc/resolv.conf
nameserver 10.96.0.10
search default.svc.cluster.local svc.cluster.local cluster.local
options ndots:5
katsurai.toshio@mf-0631-mm01:~/tk_work/learn_k8s$ kubectl run --image=centos:6 --restart=Never --rm -i testpod -- dig -x 10.111.224.170
katsurai.toshio@mf-0631-mm01:~/tk_work/learn_k8s$ `kubectl run --image=centos:6 --restart=Never --rm -i testpod -- dig _http-port._tcp.sample-clusterip.default.svc.cluster.local SRV`

katsurai.toshio@mf-0631-mm01:~/tk_work/learn_k8s$ vi sample-clusterip-vip.yml
katsurai.toshio@mf-0631-mm01:~/tk_work/learn_k8s$ kubectl apply -f sample-clusterip-vip.yml
The Service "sample-clusterip-vip" is invalid: spec.clusterIP: Invalid value: "10.111.224.170": provided IP is already allocated

katsurai.toshio@mf-0631-mm01:~/tk_work/learn_k8s$ kubectl apply -f sample-externalip.yml
katsurai.toshio@mf-0631-mm01:~/tk_work/learn_k8s$ kubectl get nodes -o custom-columns="NAME:{metadata.name},IP:{status.addresses[].address}"
NAME                 IP
docker-for-desktop   192.168.65.3
katsurai.toshio@mf-0631-mm01:~/tk_work/learn_k8s$ kubectl get services
NAME                TYPE        CLUSTER-IP       EXTERNAL-IP             PORT(S)    AGE
kubernetes          ClusterIP   10.96.0.1        <none>                  443/TCP    2d
sample-clusterip    ClusterIP   10.111.224.170   <none>                  8080/TCP   53m
sample-externalip   ClusterIP   10.103.198.89    10.240.0.7,10.240.0.8   8080/TCP   1m

katsurai.toshio@mf-0631-mm01:~/tk_work/learn_k8s$ kubectl apply -f sample-nodeport-fail.yml
The Service "sample-nodeport-fail" is invalid: spec.ports[0].nodePort: Invalid value: 8888: provided port is not in the valid range. The range of valid ports is 30000-32767
katsurai.toshio@mf-0631-mm01:~/tk_work/learn_k8s$ kubectl apply -f sample-nodeport-fail2.yml
The Service "sample-nodeport-fail" is invalid: spec.ports[0].nodePort: Invalid value: 30080: provided port is already allocated

kubectl run --image=centos:6 --restart=Never --rm -i testpod -- dig sample-headless.default.svc.cluster.local
kubectl run --image=centos:6 --restart=Never --rm -i testpod -- dig sample-externalname.default.svc.cluster.local CNAME

kubectl apply -f sample-ingress-apps.yml
kubectl exec -it sample-ingress-apps-1 -- mkdir /usr/share/nginx/html/path1/
kubectl exec -it sample-ingress-apps-1 -- cp /etc/hostname /usr/share/nginx/html/path1/index.html
kubectl exec -it sample-ingress-apps-2 -- mkdir /usr/share/nginx/html/path2/
kubectl exec -it sample-ingress-apps-2 -- cp /etc/hostname /usr/share/nginx/html/path2/index.html
kubectl exec -it sample-ingress-default -- cp /etc/hostname /usr/share/nginx/html/index.html

openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout ./tls.key -out ./tls.crt -subj "/CN=sample.example.com"
kubectl create secret tls --save-config tls-sample --key ./tls.key --cert ./tls.crt

kubectl apply -f sample-env.yml
kubectl get pods sample-env -o yaml

katsurai.toshio@mf-0631-mm01:~/tk_work/learn_k8s$ kubectl apply -f sample-env-pod.yml
katsurai.toshio@mf-0631-mm01:~/tk_work/learn_k8s$ kubectl get pods -o wide
NAME             READY     STATUS    RESTARTS   AGE       IP           NODE
sample-env-pod   1/1       Running   0          7s        10.1.0.170   docker-for-desktop
katsurai.toshio@mf-0631-mm01:~/tk_work/learn_k8s$ kubectl exec -it sample-env-pod env | grep K8S_NODE
K8S_NODE=docker-for-desktop

katsurai.toshio@mf-0631-mm01:~/tk_work/learn_k8s$ kubectl apply -f sample-env-container.yml
katsurai.toshio@mf-0631-mm01:~/tk_work/learn_k8s$ kubectl exec -it sample-env-container env | grep CPU
CPU_REQUESTS=0
CPU_LIMITS=4

katsurai.toshio@mf-0631-mm01:~/tk_work/learn_k8s$ kubectl apply -f sample-env-fail.yml
katsurai.toshio@mf-0631-mm01:~/tk_work/learn_k8s$ kubectl logs sample-env-fail
${TESTENV} ${HOSTNAME}
katsurai.toshio@mf-0631-mm01:~/tk_work/learn_k8s$ kubectl apply -f sample-env-fail2.yml
katsurai.toshio@mf-0631-mm01:~/tk_work/learn_k8s$ kubectl logs sample-env-fail2
100 $(HOSTNAME)
katsurai.toshio@mf-0631-mm01:~/tk_work/learn_k8s$ kubectl apply -f sample-env-fail3.yml
katsurai.toshio@mf-0631-mm01:~/tk_work/learn_k8s$ kubectl logs sample-env-fail3
docker-for-desktop ${K8S_NODE}

echo -n "root" > ./username
echo -n "rootpassword" > ./password
katsurai.toshio@mf-0631-mm01:~/tk_work/learn_k8s$ kubectl create secret generic --save-config sample-db-auth --from-file=./username --from-file=./password
katsurai.toshio@mf-0631-mm01:~/tk_work/learn_k8s$ kubectl get secrets sample-db-auth -o json | jq .data
{
  "password": "cm9vdHBhc3N3b3Jk",
  "username": "cm9vdA=="
}
katsurai.toshio@mf-0631-mm01:~/tk_work/learn_k8s$ kubectl get secrets sample-db-auth -o json | jq -r .data.username | base64 --decode
root

katsurai.toshio@mf-0631-mm01:~/tk_work/learn_k8s$ vi env-secret.txt
katsurai.toshio@mf-0631-mm01:~/tk_work/learn_k8s$ cat env-secret.txt
username=root
password=rootpassword
katsurai.toshio@mf-0631-mm01:~/tk_work/learn_k8s$ kubectl create secret generic --save-config sample-db-aut --from-env-file ./env-secret.txt

```
kubectl create secret docker-registry sample-registry-auth --save-config \
--docker-server=REGISTRY_SERVER \
--docker-username=REGISTRY_USER \
--docker-password=REGISTRY_USER_PASSWORD \
--docker-email=REGISTRY_USER_EMAIL
```
kubectl get secrets sample-registry-auth -o yaml | grep "\.dockerconfig" | awk -F' ' '{print $2}' | base64 --decode


