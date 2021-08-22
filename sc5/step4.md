


Для сравнения аналогичный вызов на предыдущем шаге возвращал такой ответ:

`Hello from ServiceA! Calling Producer Service... I/O error on GET request for "http://producer-internal-host:80/": producer-internal-host; nested exception is java.net.UnknownHostException: producer-internal-host`

Теперь в кластере существуют поставщик данных для ServiceA, который связан с хостом producer-internal-host, поэтому ServiceA на этом шаге получает корректный ответ.

Проверим логи доступа Envoy ingress-шлюза:
`kubectl logs -l app=istio-ingressgateway -n istio-system -c istio-proxy`{{execute}}

Проверим логи доступа Envoy в поде с бизнес сервисом ServiceA:
`kubectl logs -l app=service-a-app -c istio-proxy`{{execute}}

Проверим логи доступа Envoy в поде с бизнес сервисом ServiceB:
`kubectl logs -l app=service-b-app -c istio-proxy`{{execute}}

Перейдем далее.