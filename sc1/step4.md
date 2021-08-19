
`kubectl apply -f https://raw.githubusercontent.com/avsinsight/katacoda-scenarios/main/sc1/src/serviceA-v1-deployment.yml`{{execute}}

https://kubernetes.io/docs/reference/kubernetes-api/workload-resources/deployment-v1/
https://kubernetes.io/docs/reference/kubernetes-api/workload-resources/pod-v1/#Container

`kubectl apply -f https://github.com/avsinsight/katacoda-scenarios/blob/main/sc1/src/serviceA-srv.yml`{{execute}}
`kubectl apply -f https://raw.githubusercontent.com/avsinsight/katacoda-scenarios/main/sc1/src/serviceA-gw.yml`{{execute}}
`kubectl apply -f https://raw.githubusercontent.com/avsinsight/katacoda-scenarios/main/sc1/src/inbound-to-serviceA-vs.yml`{{execute}}




