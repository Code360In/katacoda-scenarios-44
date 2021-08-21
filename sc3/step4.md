На данном шаге мы откроем исходящий HTTPS трафик из service mesh для получения ответов из oracle.com на запросы из ServiceG.

Рассмотрим манифест outbound-oracle-dr.yml:
`https://raw.githubusercontent.com/avsinsight/katacoda-scenarios/main/sc3/src/outbound-oracle-dr.yml`{{copy}}

Обратите внимание на ключ spec.trafficPolicy.portLevelSettings[0].tls со значением `mode: SIMPLE`. Такое значение позволит зашифровать HTTP трафик, поступивший на указанный порт 80 для хоста www.oracle.com.

Рассмотрим манифест oracle-host-se.yml:
`https://raw.githubusercontent.com/avsinsight/katacoda-scenarios/main/sc3/src/oracle-host-se.yml`{{copy}}

Ключи spec.hosts, а также две пары ключей number и protocol обозначают допустимые протоколы для приведенного хоста, но обратите внимание но ключ spec.ports[0].targetPort (443). При налчии этого ключа, трафик, который поступит на порт в значении spec.ports[0].number (80), будет перенаправлен на порт в значении spec.ports[0].targetPort (443).

Таким образом мы достигнем перенаправления трафика при помощи envoy-прокси в контейнере с бизнес сервисом из порта 80, куда направляет запросы ServiceG, в порт 443.

Применим DestinationRule:
`kubectl apply -f https://raw.githubusercontent.com/avsinsight/katacoda-scenarios/main/sc3/src/outbound-oracle-dr.yml`{{execute}}

Применим ServiceEntry:
`kubectl apply -f https://raw.githubusercontent.com/avsinsight/katacoda-scenarios/main/sc3/src/oracle-host-se.yml`{{execute}}

Cовершим GET запрос по адресу ingress-шлюза:
`curl -v http://$GATEWAY_URL/service-g`{{execute}}

Таким образом мы зашифровали HTTP трафик и создали HTTPS соединение.