На данном шаге мы откроем исходящий HTTPS трафик из service mesh для получения ответов из oracle.com на запросы из ServiceG.

Рассмотрим манифест outbound-oracle-dr.yml:
`https://raw.githubusercontent.com/avsinsight/katacoda-scenarios/main/sc3/src/outbound-oracle-dr.yml`{{copy}}

Обратите внимание на ключ spec.trafficPolicy.portLevelSettings[0].tls со значением `mode: SIMPLE`. Такое значение позволит зашифровать HTTP трафик, поступивший на указанный порт 80 для хоста www.oracle.com.

Рассмотрим манифест oracle-host-se.yml:
`https://raw.githubusercontent.com/avsinsight/katacoda-scenarios/main/sc3/src/oracle-host-se.yml`{{copy}}

Ключи spec.hosts, а также две пары ключей number и protocol обозначают допустимые протоколы для приведенного хоста, но обратите внимание на ключ spec.ports[0].targetPort (443). При налчии этого ключа, трафик, который поступит на порт в значении spec.ports[0].number (80), будет перенаправлен на порт в значении spec.ports[0].targetPort (443).

Таким образом мы достигнем перенаправления трафика при помощи envoy-прокси в поде с бизнес сервисом из порта 80, куда направляет запросы ServiceG, в порт 443.

Применим DestinationRule:
`kubectl apply -f https://raw.githubusercontent.com/avsinsight/katacoda-scenarios/main/sc3/src/outbound-oracle-dr.yml`{{execute}}

Применим ServiceEntry:
`kubectl apply -f https://raw.githubusercontent.com/avsinsight/katacoda-scenarios/main/sc3/src/oracle-host-se.yml`{{execute}}

Cовершим GET запрос по адресу ingress-шлюза:
`curl -v http://$GATEWAY_URL/service-g`{{execute}}

В ответе мы получим:
`Hello from ServiceG! Calling master system API... Received response from master system (http://www.oracle.com/index.html): <!DOCTYPE html><html lang="en"><head><link href="/product-navigator/main__product-navigator__1.29.44.css" as="style" rel="preload"/><meta charSet="utf-8"/><title>Oracle ...`

Как мы убедились ранее на шаге 2, данную страницу можно получить только при GET запросе с применением HTTPS протокола.

Хотел бы также отметить, что все сетевая логика связанная с созданием HTTPS соединения и шифрования данных, осталась абсолютно прозрачной для бизнес сервиса, который продолжал совершать небезопасные HTTP запросы без TLS шифрования.

Таким образом мы зашифровали HTTP трафик и создали HTTPS соединение.