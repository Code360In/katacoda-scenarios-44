На этом шаге мы применим политику ограничения числа хапросов в единицу времени (rate limit, RL-полтика) на уровне отдельного envoy-прокси в поде с бизнес серсом.

Для этого мы создадим EnvoyFilter, который будет внедрен в цепь фильтров envoy-прокси. 

Рассмотрим манифест rate-limits-ef.yml:
`https://raw.githubusercontent.com/avsinsight/katacoda-scenarios/main/sc4/src/rate-limits-ef.yml`{{copy}}

Давайте применим этот манифест:
`kubectl apply -f https://raw.githubusercontent.com/avsinsight/katacoda-scenarios/main/sc4/src/rate-limits-ef.yml`{{execute}}


Повторите запрос по адресу ingress-шлюза. Соврешите несколько запросов с промежутком 2, 5 и 15 секунд:
`curl -v http://$GATEWAY_URL/service-g`{{execute}}

Таким образом, мы примменили политику ограничения числа вхоядщих запросов в единицу времени. 