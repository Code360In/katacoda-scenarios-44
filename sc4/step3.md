На этом шаге мы применим политику ограничения числа запросов в единицу времени (rate limit) на уровне отдельного envoy-прокси в поде с бизнес сервисом.

Для этого мы создадим EnvoyFilter, который будет внедрен в цепь фильтров envoy-прокси.

Рассмотрим манифест rate-limits-ef.yml:
`https://raw.githubusercontent.com/avsinsight/katacoda-scenarios/main/sc4/src/rate-limits-ef.yml`{{copy}}

Давайте применим этот манифест:
`kubectl apply -f https://raw.githubusercontent.com/avsinsight/katacoda-scenarios/main/sc4/src/rate-limits-ef.yml`{{execute}}

Повторите запрос по адресу ingress-шлюза. Совершите несколько запросов с промежутком 2, 5 и 15 секунд:
`curl -v http://$GATEWAY_URL/service-b`{{execute}}

Таким образом, мы применили политику ограничения числа входящих запросов в единицу времени. 