В предидущем шаге обращались к ServiceB через цепь взаимодействий: ingress-шлюз -> ServiceA -> ServiceB. Напомню, каждый сервис в это цепи обращается на IP-адресс и номер порта вызываемого сервиса, который декларированы в манифесте Service. Такие прямые вызовы могут осуществлять любые клинета, находящиеся в том же сетевом контуре, например внутри кластера, или вне кластера - если IP-адрес доступен из-вне.

Давайте соврешим прямой вызов ServiceB, миную ingress-шлюз и ServiceA.

Получим краткое описание существующих сервисов Kubernetes в текущем пространстве имен:
`kubectl get services`{{execute}}

Для удобства работы экспортируем в переменные IP-адрес и номер порта эндпоинта, который декларирован для ServiceB в манифесте Service с именем producer-internal-host:
`export SERVICEB_HOST=$(kubectl get svc producer-internal-host -o jsonpath='{.spec.clusterIP}') && export SERVICEB_PORT=$(kubectl get svc producer-internal-host -o jsonpath='{.spec.ports[?(@.name=="http-80")].port}')`{{execute}}

Просмотрим IP-адрес и номер пората ServiceB
`echo $SERVICEB_HOST:$SERVICEB_PORT`{{execute}}

Совершим прямой вызов ServiceB миную ingress-шлюз и ServiceA:
`curl -v http://$SERVICEB_HOST:$SERVICEB_PORT/`{{execute}}

В ответе мы увидим:
`Hello from ServiceB!`

Теперь давайте применим правило аутентификации для запрета доступа к поду с селектоом app:service-b-app в пространстве имен dev-service-mesh, всем узлам, которые не подтвердят допутимую идентичность:
```
apiVersion: security.istio.io/v1beta1
kind: PeerAuthentication
metadata:
  name: service-b-peer-to-peer-authn-policy
  namespace: dev-service-mesh
spec:
  selector:
    matchLabels:
      app: service-b-app
  portLevelMtls:
    8082:
      mode: STRICT
```

Применим даный манифест:
`kubectl apply -f https://raw.githubusercontent.com/avsinsight/katacoda-scenarios/main/sc5/src/service-b-peer-to-peer-authn-policy.yml`{{execute}}

Повторим прямой вызов ServiceB миную ingress-шлюз и ServiceA:
`curl -v http://$SERVICEB_HOST:$SERVICEB_PORT/`{{execute}}

Теперь ответа от ServiceB не будет, а выводе CURL мы увидим обозначения отклонения соединения вызываемым узлом:
```
* Recv failure: Connection reset by peer
* stopped the pause stream!
* Closing connection 0
curl: (56) Recv failure: Connection reset by peer
```

Что произошло?

После примнения политики аутентификации узла под с селектоом app:service-b-app отклонил вызовы из неаутентифицированного узла, в роли которого выступил в данном слеучае клинет CURL.

Повторим вызов ServiceB через цепь взаимодействий ingress-шлюз -> ServiceA -> ServiceB. Для этого соврешим GET запрос в адрес ingress-шлюза, как ранее:
`curl -v http://$GATEWAY_URL/service-a`{{execute}}

Обратите внимание на то, что ответ при таком вызове не изменился, так как Istio обеспечивает идентификацию узлов внутри service mesh, которая рассмотрена в теоритической части курса в разделе "Конфигурация безопасности сети".