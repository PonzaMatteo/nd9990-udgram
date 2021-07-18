# Screenshots
To help review your infrastructure, please include the following screenshots in this directory::

## Deployment Pipeline
- [x] DockerHub showing containers that you have pushed
- [x] GitHub repository’s settings showing your Travis webhook (can be found in Settings - Webhook)
* [x] Travis CI showing a successful build and deploy job

## Kubernetes
* To verify Kubernetes pods are deployed properly
```bash
❯ kubectl get pods
NAME                           READY   STATUS    RESTARTS   AGE
api-feed-7774dfb77d-785cw      1/1     Running   0          9m28s
api-feed-7774dfb77d-f5z6b      1/1     Running   0          9m28s
api-feed-7774dfb77d-mltg7      1/1     Running   0          9m28s
api-user-6c7db85759-l9bz8      1/1     Running   0          9m28s
api-user-6c7db85759-spsnh      1/1     Running   0          9m28s
api-user-6c7db85759-sqxx5      1/1     Running   0          9m28s
frontend-78f5d5f544-slrkw      1/1     Running   0          9m20s
frontend-78f5d5f544-tpjxp      1/1     Running   0          9m20s
frontend-78f5d5f544-v72zg      1/1     Running   0          9m20s
reverseproxy-f8579f45f-86p62   1/1     Running   0          16s
```
* To verify Kubernetes services are properly set up
```bash
❯ kubectl describe services
Name:              api-feed-svc
Namespace:         default
Labels:            app=api-feed
Annotations:       <none>
Selector:          app=api-feed
Type:              ClusterIP
IP:                10.100.27.243
Port:              <unset>  8080/TCP
TargetPort:        8080/TCP
Endpoints:         192.168.26.220:8080,192.168.30.231:8080,192.168.80.170:8080
Session Affinity:  None
Events:            <none>


Name:              api-user-svc
Namespace:         default
Labels:            app=api-user
Annotations:       <none>
Selector:          app=api-user
Type:              ClusterIP
IP:                10.100.211.11
Port:              <unset>  8080/TCP
TargetPort:        8080/TCP
Endpoints:         192.168.20.128:8080,192.168.64.152:8080,192.168.84.222:8080
Session Affinity:  None
Events:            <none>


Name:              kubernetes
Namespace:         default
Labels:            component=apiserver
                   provider=kubernetes
Annotations:       <none>
Selector:          <none>
Type:              ClusterIP
IP:                10.100.0.1
Port:              https  443/TCP
TargetPort:        443/TCP
Endpoints:         192.168.109.246:443,192.168.187.251:443
Session Affinity:  None
Events:            <none>
```
* To verify that you have horizontal scaling set against CPU usage
```bash
kubectl describe hpa
Name:                                                  api-feed
Namespace:                                             default
Labels:                                                <none>
Annotations:                                           <none>
CreationTimestamp:                                     Sun, 18 Jul 2021 17:15:58 +0800
Reference:                                             Deployment/api-feed
Metrics:                                               ( current / target )
  resource cpu on pods  (as a percentage of request):  <unknown> / 50%
Min replicas:                                          1
Max replicas:                                          5
Deployment pods:                                       3 current / 0 desired
Conditions:
  Type           Status  Reason                   Message
  ----           ------  ------                   -------
  AbleToScale    True    SucceededGetScale        the HPA controller was able to get the targets current scale
  ScalingActive  False   FailedGetResourceMetric  the HPA was unable to compute the replica count: unable to get metrics for resource cpu: unable to fetch metrics from resource metrics API: the server could not find the requested resource (get pods.metrics.k8s.io)
Events:
  Type     Reason                        Age                   From                       Message
  ----     ------                        ----                  ----                       -------
  Warning  FailedComputeMetricsReplicas  8m33s (x12 over 11m)  horizontal-pod-autoscaler  invalid metrics (1 invalid out of 1), first error is: failed to get cpu utilization: unable to get metrics for resource cpu: unable to fetch metrics from resource metrics API: the server could not find the requested resource (get pods.metrics.k8s.io)
  Warning  FailedGetResourceMetric       77s (x41 over 11m)    horizontal-pod-autoscaler  unable to get metrics for resource cpu: unable to fetch metrics from resource metrics API: the server could not find the requested resource (get pods.metrics.k8s.io)


Name:                                                  api-user
Namespace:                                             default
Labels:                                                <none>
Annotations:                                           <none>
CreationTimestamp:                                     Sun, 18 Jul 2021 17:15:58 +0800
Reference:                                             Deployment/api-user
Metrics:                                               ( current / target )
  resource cpu on pods  (as a percentage of request):  <unknown> / 50%
Min replicas:                                          1
Max replicas:                                          5
Deployment pods:                                       3 current / 0 desired
Conditions:
  Type           Status  Reason                   Message
  ----           ------  ------                   -------
  AbleToScale    True    SucceededGetScale        the HPA controller was able to get the target's current scale
  ScalingActive  False   FailedGetResourceMetric  the HPA was unable to compute the replica count: unable to get metrics for resource cpu: unable to fetch metrics from resource metrics API: the server could not find the requested resource (get pods.metrics.k8s.io)
Events:
  Type     Reason                        Age                   From                       Message
  ----     ------                        ----                  ----                       -------
  Warning  FailedComputeMetricsReplicas  8m33s (x12 over 11m)  horizontal-pod-autoscaler  invalid metrics (1 invalid out of 1), first error is: failed to get cpu utilization: unable to get metrics for resource cpu: unable to fetch metrics from resource metrics API: the server could not find the requested resource (get pods.metrics.k8s.io)
  Warning  FailedGetResourceMetric       77s (x41 over 11m)    horizontal-pod-autoscaler  unable to get metrics for resource cpu: unable to fetch metrics from resource metrics API: the server could not find the requested resource (get pods.metrics.k8s.io)

```
* To verify that you have set up logging with a backend application
```bash
kubectl logs {pod_name}


> udagram-api@2.0.0 prod /usr/src/app
> tsc && node ./www/server.js

Executing (default): CREATE TABLE IF NOT EXISTS "FeedItem" ("id"   SERIAL , "caption" VARCHAR(255), "url" VARCHAR(255), "createdAt" TIMESTAMP WITH TIME ZONE, "updatedAt" TIMESTAMP WITH TIME ZONE, PRIMARY KEY ("id"));
Executing (default): SELECT i.relname AS name, ix.indisprimary AS primary, ix.indisunique AS unique, ix.indkey AS indkey, array_agg(a.attnum) as column_indexes, array_agg(a.attname) AS column_names, pg_get_indexdef(ix.indexrelid) AS definition FROM pg_class t, pg_class i, pg_index ix, pg_attribute a WHERE t.oid = ix.indrelid AND i.oid = ix.indexrelid AND a.attrelid = t.oid AND t.relkind = 'r' and t.relname = 'FeedItem' GROUP BY i.relname, ix.indexrelid, ix.indisprimary, ix.indisunique, ix.indkey ORDER BY i.relname;
starting server
server running undefined
press CTRL+C to stop server
Executing (default): SELECT count(*) AS "count" FROM "FeedItem" AS "FeedItem";
Executing (default): SELECT "id", "caption", "url", "createdAt", "updatedAt" FROM "FeedItem" AS "FeedItem" ORDER BY "FeedItem"."id" DESC;
```

