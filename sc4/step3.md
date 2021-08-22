На этом шаге мы применим политику ограничения числа запросов в единицу времени (rate limit) на уровне отдельного envoy-прокси в поде с бизнес сервисом.

Для этого мы создадим EnvoyFilter, который будет внедрен в цепь фильтров envoy-прокси.

Рассмотрим манифест rate-limits-ef.yml:
`https://raw.githubusercontent.com/avsinsight/katacoda-scenarios/main/sc4/src/rate-limit-5-ef.yml`{{copy}}

Ключи metadata.namespace и spec.workloadSelector.labels позволяют определить под, envoy-прокси которого должен применить конфигурации.

Ключ spec.configPatches[0].patch.value содержит фильтр в формате API Envoy.

Ключ spec.configPatches[0].patch.value.typed_config.token_bucket содержит ключи и значения, позволяющие настроить частоту допустимых запросов, представляет из себя абстрактную "корзину токенов" или квот, также является реализацией алгоритма "Текущего ведра" или "Дырявого ведра". Каждый поступающий запрос потребляет по одному токену.

Рассмотрим ключи token_bucket:

spec.configPatches[0].patch.value.typed_config.token_bucket.fill_interval - содержит значение временного интервала в секундах, по истечению которого, корзина наполняется новыми токенами (выдаются очередные квоты на обработку запроса)

spec.configPatches[0].patch.value.typed_config.token_bucket.max_tokens - максимальное число токенов, которая корзина может вместить, также является изначальным числом токенов

spec.configPatches[0].patch.value.typed_config.token_bucket.tokens_per_fill - чило токенов, которое добавляется при истечении временного интервала.

Таким образом, для ограничения запросов до 100 tps (запросов в секунду), следует указать:
`max_tokens: 100`
`tokens_per_fill: 100`
`fill_interval: 1s`

Манифест в текущем виде ограничит число запросов до 1 в 5 секунд.

Давайте применим этот манифест:
`kubectl apply -f https://raw.githubusercontent.com/avsinsight/katacoda-scenarios/main/sc4/src/rate-limit-5-ef.yml`{{execute}}

Повторите запрос по адресу ingress-шлюза. Совершите несколько запросов с промежутком 1 секунду:
`curl -v http://$GATEWAY_URL/service-b`{{execute}}

При превышении допустимой частоты 1 запрос в 5 секунд, мы будем видеть слудующие заголовки в ответе, тела ответа не будет:
`< HTTP/1.1 429 Too Many Requests`
`< x-local-rate-limit: true`
`< content-length: 18
`< content-type: text/plain`
`< date: Sat, 21 Aug 2021 23:32:16 GMT`
`< server: istio-envoy`
`< x-envoy-upstream-service-time: 0`

Таким образом, мы применили политику ограничения числа входящих запросов в единицу времени. 