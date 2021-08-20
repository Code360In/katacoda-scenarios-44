На этом шаге мы применим манифест Deploymnet на основе чего будет запущен под, содержащий бизнес-сервис.

Давайте применим манифест, выполнив команду:`kubectl apply -f https://raw.githubusercontent.com/avsinsight/katacoda-scenarios/main/sc1/src/service-b-deployment.yml`{{execute}}

Давайте рассмотрим этот манифест: `kubectl get deploy service-b-deployment -o yaml`{{execute}}


Рассмотрим основые строки

Создание сервиса
`kubectl apply -f https://raw.githubusercontent.com/avsinsight/katacoda-scenarios/main/sc1/src/serviceA-srv.yml`{{execute}}

Получим манифест:

Рассмотрим основые строки

Открытие входящего трафика

Создание Gateway

`kubectl apply -f https://raw.githubusercontent.com/avsinsight/katacoda-scenarios/main/sc1/src/serviceA-gw.yml`{{execute}}

Получим манифест:

Рассмотрим основые строки


Создание VS
`kubectl apply -f https://raw.githubusercontent.com/avsinsight/katacoda-scenarios/main/sc1/src/inbound-to-serviceA-vs.yml`{{execute}}

Получим манифест:

Рассмотрим основые строки

Доступ к ingress шлюзу
Объснить:
`kubectl get svc istio-ingressgateway -n istio-system`{{execute}}
`export INGRESS_HOST=$(kubectl -n istio-system get service istio-ingressgateway -o jsonpath='{.status.loadBalancer.ingress[0].ip}')`{{execute}}
`export INGRESS_PORT=$(kubectl -n istio-system get service istio-ingressgateway -o jsonpath='{.spec.ports[?(@.name=="http2")].port}')`{{execute}}
`export GATEWAY_URL=$INGRESS_HOST:$INGRESS_PORT`{{execute}}

Убедимся что, все поды работают корректно:
`kubectl get pods --all-namespaces `{{execute}}

`curl -v http://$GATEWAY_URL/service-a`{{execute}}


Получение информации о сервисах

Просмотрим логи:

`kubectl logs -l app=service-a-app -c istio-proxy`{{execute}}
`kubectl logs istio-ingressgateway-5c77558485-rw6hd`{{execute}} -- заменить на вид выше


`kubectl apply -f https://raw.githubusercontent.com/avsinsight/katacoda-scenarios/main/sc1/src/service-b-deployment.yml`{{execute}}
Создадим сервис: `kubectl apply -f https://raw.githubusercontent.com/avsinsight/katacoda-scenarios/main/sc1/src/producer-internal-host.yml`{{execute}}
`kubectl apply -f https://raw.githubusercontent.com/avsinsight/katacoda-scenarios/main/sc1/src/producer-internal-host-vs.yml`{{execute}}

`curl -v http://$GATEWAY_URL/service-a`{{execute}}

Создадим сервис С
`kubectl apply -f https://raw.githubusercontent.com/avsinsight/katacoda-scenarios/main/sc1/src/service-c-deployment.yml`{{execute}}
`kubectl apply -f https://raw.githubusercontent.com/avsinsight/katacoda-scenarios/main/sc1/src/service-c-srv.yml`{{execute}}
Обновим вирутальный сервис:
`kubectl apply -f https://raw.githubusercontent.com/avsinsight/katacoda-scenarios/main/sc1/src/producer-internal-host-50-c-vs.yml`{{execute}}
Приблизительно 50% запросов будут направлены на Service C, оставшиейся как и ранее - на Service B. Совершите 5-6 и убедить что в ответад данных из разных сервисов.
`curl -v http://$GATEWAY_URL/service-a`{{execute}}

Конфигруация исходящего трафика
Существует 3 подхода: 
1) откруть доступ из любого пода по умолчанию
2) Закрыть доступ на любой внешний хост исключаю те, которые явно указаны в SE 
3) Направив трафик через единый egress шлюз: Egress gateways allow you to apply Istio features, for example, monitoring and route rules, to traffic exiting the mesh. 

Развернем egress-шлюз : `istioctl install --set components.egressGateways[0].name=istio-egressgateway --set components.egressGateways[0].enabled=true`{{execute}}

`kubectl apply -f https://raw.githubusercontent.com/avsinsight/katacoda-scenarios/main/sc1/src/worldtime-host-se.yml`{{execute}}
`kubectl apply -f https://raw.githubusercontent.com/avsinsight/katacoda-scenarios/main/sc1/src/worldtime-gw.yml`{{execute}}
`kubectl apply -f https://raw.githubusercontent.com/avsinsight/katacoda-scenarios/main/sc1/src/outbound-srv-c-to-worldtime-vs.yml`{{execute}}
Теперь исходящий трафик направляется через egress-шлюз:
`curl -v http://$GATEWAY_URL/service-a`{{execute}}
Проверим логи Envoy egress-шлюза:
`kubectl logs -l istio=egressgateway -c istio-proxy -n istio-system | tail`{{execute}}

Переведем 100% из Service A в Service C:
`kubectl apply -f https://raw.githubusercontent.com/avsinsight/katacoda-scenarios/main/sc1/src/producer-internal-host-100-c-vs.yml`{{execute}}
Проверим:
`curl -v http://$GATEWAY_URL/service-a`{{execute}}




kubectl get pods --all-namespacesNAMESPACE          NAME                                       READY   STATUS    RESTARTS   AGE
dev-service-mesh   service-a-v1-deployment-7d449f9498-z8vml   2/2     Running   0          93s
istio-system       istio-ingressgateway-5c77558485-rw6hd      1/1     Running   0          3m31s
istio-system       istiod-6c565d48b8-xt4zs                    1/1     Running   0          3m55s
kube-system        coredns-66bff467f8-fccv2                   1/1     Running   0          5m57s
kube-system        coredns-66bff467f8-qrcrn                   1/1     Running   0          5m57s
kube-system        etcd-controlplane                          1/1     Running   0          6m6s
kube-system        katacoda-cloud-provider-688986cfb9-2vhrf   1/1     Running   3          5m56s
kube-system        kube-apiserver-controlplane                1/1     Running   0          6m6s
kube-system        kube-controller-manager-controlplane       1/1     Running   0          6m6s
kube-system        kube-flannel-ds-amd64-4d8v7                1/1     Running   0          5m42s
kube-system        kube-flannel-ds-amd64-hsq28                1/1     Running   0          5m57s
kube-system        kube-keepalived-vip-sj9xt                  1/1     Running   0          4m58s
kube-system        kube-proxy-d57lp                           1/1     Running   0          5m42s



`kubectl logs <pod-name>`

`kubectl logs istio-ingressgateway-5c77558485-rw6hd`

`kubectl logs service-a-v1-deployment-7d449f9498-z8vml`
.   ____          _            __ _ _
/\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
\\/  ___)| |_)| | | | | || (_| |  ) ) ) )
'  |____| .__|_| |_|_| |_\__, | / / / /
=========|_|==============|___/=/_/_/_/
:: Spring Boot ::                (v2.5.3)

2021-08-19 14:27:25.622  INFO 1 --- [           main] com.example.springboot.Application       : Starting Application using Java 1.8.0_212 on service-a-v1-deployment-7d449f9498-z8vml with PID 1 (/app.jar started by ? in /)
2021-08-19 14:27:25.627  INFO 1 --- [           main] com.example.springboot.Application       : No active profile set, falling back to default profiles: default
2021-08-19 14:27:28.539  INFO 1 --- [           main] o.s.b.w.embedded.tomcat.TomcatWebServer  : Tomcat initialized with port(s): 8081 (http)
2021-08-19 14:27:28.571  INFO 1 --- [           main] o.apache.catalina.core.StandardService   : Starting service [Tomcat]
2021-08-19 14:27:28.572  INFO 1 --- [           main] org.apache.catalina.core.StandardEngine  : Starting Servlet engine: [Apache Tomcat/9.0.50]
2021-08-19 14:27:28.695  INFO 1 --- [           main] o.a.c.c.C.[Tomcat].[localhost].[/]       : Initializing Spring embedded WebApplicationContext
2021-08-19 14:27:28.695  INFO 1 --- [           main] w.s.c.ServletWebServerApplicationContext : Root WebApplicationContext: initialization completed in 2901 ms
2021-08-19 14:27:30.262  INFO 1 --- [           main] o.s.b.w.embedded.tomcat.TomcatWebServer  : Tomcat started on port(s): 8081 (http) with context path ''
2021-08-19 14:27:30.287  INFO 1 --- [           main] com.example.springboot.Application       : Started Application in 5.924 seconds (JVM running for 8.194)






https://istio.io/latest/docs/setup/getting-started/

https://www.katacoda.com/artashesavetisyan/scenarios/sc1

https://www.katacoda.com/courses/kubernetes/networking-introduction

https://www.katacoda.com/courses/kubernetes/guestbook - пример удобного интерфейса 

https://istio.io/latest/docs/tasks/traffic-management/egress/egress-gateway/