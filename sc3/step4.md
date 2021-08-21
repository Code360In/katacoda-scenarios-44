На данном шаге мы откроем исходящий HTTPS трафик из service mesh для получения ответов из google.com на запросы из ServiceC.

Развернем egress-шлюз, выполнив команду авто-конфигруации Isto:

`istioctl install --set components.egressGateways[0].name=istio-egressgateway --set components.egressGateways[0].enabled=true`{{execute}}

Просмотрим манифест google-host-se.yml:
`https://raw.githubusercontent.com/avsinsight/katacoda-scenarios/main/sc3/src/google-host-se.yml`{{copy}}

Ключи spec.hosts, spec.ports[0].number, spec.ports[0].protocol содержат значение имени хоста, запросы на который следует разрешить, номера его порта, вид протокола.

Применим ServiceEntry:
`kubectl apply -f https://raw.githubusercontent.com/avsinsight/katacoda-scenarios/main/sc3/src/google-host-se.yml`{{execute}}




egress-gw.yml
outbound-egress-google-dr.yml


https://istio.io/latest/blog/2018/egress-mongo/






Создадим манифест Gateway для исходящего трафика:
`kubectl apply -f https://raw.githubusercontent.com/avsinsight/katacoda-scenarios/main/sc1/src/worldtime-gw.yml`{{execute}}

Рассмотрим новое правило маршрутизации:
`https://raw.githubusercontent.com/avsinsight/katacoda-scenarios/main/sc1/src/outbound-srv-c-to-worldtime-vs.yml`{{copy}}

В соответствии с этим манифестом новое правило будет работать при вызовах на хост worldtimeapi.org из шлюза istio-egressgateway, а также из любого envoy-прокси в неймспейсе. Если вызов прийдет из любого envoy-прокси в неймспейсе (кроме istio-egressgateway), произойдет его перенаправление на хост istio-egressgateway. Если поступит запрос из istio-egressgateway, то он будет направлен на хост worldtimeapi.org. Таким образом достигается сосредоточение всех исходящих вызовов в кластере на шлюз istio-egressgateway.

Применим это правило:
`kubectl apply -f https://raw.githubusercontent.com/avsinsight/katacoda-scenarios/main/sc1/src/outbound-srv-c-to-worldtime-vs.yml`{{execute}}

Теперь исходящий трафик направляется через egress-шлюз и достигает worldtimeapi.org.

Совершим несколько запросов на ingress-шлюз, напомню, запросы из ServiceA все также балансируются между ServiceB и ServiceC:

`curl -v http://$GATEWAY_URL/service-a`{{execute}}

На этом шаге все ответы должны быть успешные и иметь вид (если поступили из ServiceB):
`Hello from ServiceA! Calling Producer Service... Received response from Producer Service: Hello from ServiceB!`

Или из ServiceC:
`Hello from ServiceA! Calling Producer Service... Received response from Producer Service: Hello from ServiceC! Calling worldtimeapi.org API... Received response from worldtimeapi.org: Europe/Amsterdam`
`Europe/Andorra`
`Europe/Astrakhan`
`Europe/Athens`
`Europe/Belgrade`
`Europe/Berlin`
`Europe/... (printed only 100 symbols from response body beginning)`

Обратите внимание, что в части ответа из ServiceC присутвует ответ из worldtimeapi.org по запросу `http://worldtimeapi.org/api/timezone/Europe`

Перейдем далее.