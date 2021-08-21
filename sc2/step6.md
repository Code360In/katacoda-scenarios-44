На данном шаге мы откроем исходящий трафик из service mesh для получения ответов из worldtimeapi.org на запросы из ServiceC.

Схема service mesh, в соотвесвтии с которой будем настраивать наш кластер:

`https://raw.githubusercontent.com/avsinsight/katacoda-scenarios/main/assets/sc2-4.png`{{copy}}

Существует 3 подхода к открытию исходящего трафика в Istio:

1) открытый доступ из любого пода на любой внешний хост по умолчанию - удобный подход для разработки но не безопасный и не контролируемый, поэтому в промышленной эксплуатации применяется редко.

2) Отсутствие доступа на любой внешний хост исключая те, которые явно указаны в манифесте ServiceEntry.

3) Направление трафика на внешний хост через единый egress шлюз - позволяет обогатить весь исходящий трафик из кластера требуемой логикой (например обогатить заголовками для аутентификации запросов), мониторировать и контролировать его. Данный подход применяться в больших промышленных системах.

Реализуем третий подход.

Развернем egress-шлюз, выполнив команду авто-конфигруации Isto:

`istioctl install --set components.egressGateways[0].name=istio-egressgateway --set components.egressGateways[0].enabled=true`{{execute}}

Просмотрим манифест worldtime-host-se.yml:
`https://raw.githubusercontent.com/avsinsight/katacoda-scenarios/main/sc1/src/worldtime-host-se.yml`{{copy}}

Ключи spec.hosts, spec.ports[0].number, spec.ports[0].protocol содержат значение имени хоста, запросы на который следует разрешить, номера его порта, вид протокола.

Применим ServiceEntry:
`kubectl apply -f https://raw.githubusercontent.com/avsinsight/katacoda-scenarios/main/sc1/src/worldtime-host-se.yml`{{execute}}

Создадим манифест Gateway для исходящего трафика:
`kubectl apply -f https://raw.githubusercontent.com/avsinsight/katacoda-scenarios/main/sc1/src/worldtime-gw.yml`{{execute}}

Рассмотрим новое правило маршрутизации:
`https://raw.githubusercontent.com/avsinsight/katacoda-scenarios/main/sc1/src/outbound-srv-c-to-worldtime-vs.yml`{{copy}}

В соответствии с этим манифестом новое правило будет работать при вызовах на хост worldtimeapi.org из шлюза istio-egressgateway, а также из любого envoy-прокси в неймспейсе. Если вызов приходит из любого envoy-прокси в неймспейсе (кроме istio-egressgateway), произойдет его перенаправление на хост istio-egressgateway. Если поступит запрос из istio-egressgateway, то он будет направлен на хост worldtimeapi.org. Таким образом достигается сосредоточение всех исходящих вызовов в кластере на шлюз istio-egressgateway.

Применим это правило:
`kubectl apply -f https://raw.githubusercontent.com/avsinsight/katacoda-scenarios/main/sc1/src/outbound-srv-c-to-worldtime-vs.yml`{{execute}}

Теперь исходящий трафик направляется через egress-шлюз и достигает worldtimeapi.org.

Совершим несколько запросов на ingress-шлюз, напомню, запросы из ServiceA все также балансируются между ServiceB и ServiceC:

`curl -v http://$GATEWAY_URL/service-a`{{execute}}

На этом шаге все ответы должны быть успешные вида:

Перейдем далее.