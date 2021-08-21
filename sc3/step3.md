На этом шаге мы установим ServiceG настроим service mesh согласно следующей схеме:

`https://raw.githubusercontent.com/avsinsight/katacoda-scenarios/main/assets/sc3-1.png`{{copy}}

Давайте установим ServiceG:
`kubectl apply -f https://raw.githubusercontent.com/avsinsight/katacoda-scenarios/main/sc3/src/service-g-deployment.yml`{{execute}}

Применим Service для деплоймента выше:
`kubectl apply -f https://raw.githubusercontent.com/avsinsight/katacoda-scenarios/main/sc3/src/service-g-srv.yml`{{execute}}

Создадим Gateway для маршрутизации запросов из ingress-шлюза в ServiceG:
`kubectl apply -f https://raw.githubusercontent.com/avsinsight/katacoda-scenarios/main/sc3/src/service-g-gw.yml`{{execute}}

И применим правило маршрутизации:
`kubectl apply -f https://raw.githubusercontent.com/avsinsight/katacoda-scenarios/main/sc3/src/inbound-to-service-g-vs.yml`{{execute}}

Проверим готовность подов:
`kubectl get pods --all-namespaces`{{execute}}

Все поды, за исключением katacoda-cloud-provider, должны иметь статус Running, дождитесь нужного статсуса (в зависисмоти от нагрузки на серверы Katacoda это время может сильно варьировать).

И наконец совершим GET запрос по адресу ingress-шлюза:
`curl -v http://$GATEWAY_URL/service-g`{{execute}}


В ответ на совершенный вызов на данном шаге мы должны видеть сообщение:






`Hello from ServiceA! Calling Producer Service... Received response from Producer Service: Hello from ServiceC! Calling worldtimeapi.org API... 502 Bad Gateway: [no body]`

Такой ответ - результат направления запроса из ServiceA в ServiceC, который пытается получить данные из своего поставщика Интернете, напомню этот сервис запрашивает `http://worldtimeapi.org/api/timezone/Europe`.

Однако, на данном шаге исходящие запросы из нашего кластера запрещены, поэтому в ответе мы видим `502 Bad Gateway: [no body]`.



Перейдем далее.