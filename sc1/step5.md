Манифест Service позволяет Kubernetes создать некую абстракцию над коллекцией подов и открыть к ним единый адрес доступа, закрепив за ними определенный хост и IP адрес.

Давайте рассмотрим этот манифест:

`https://raw.githubusercontent.com/avsinsight/katacoda-scenarios/main/sc1/src/producer-internal-host.yml`{{copy}}

Обратите внимание на значение ключа metadata.name - содержит имя хоста по которому будет доступно внутри service mesh приложение, имя которого указно в ключе spec.selector.app.

Также следует учесть, что spec.ports[0].port (80) содержит номер порта на котором будет доступен хост из metadata.name, но трафик далее будет направлен на порт, указанный в значении ключа spec.ports[0].targetPort

Даввайте применим этот манифест:

`kubectl apply -f https://raw.githubusercontent.com/avsinsight/katacoda-scenarios/main/sc1/src/producer-internal-host.yml`{{execute}}

Получим его детальное описание из cubectl:

`kubectl describe service producer-internal-host`{{execute}}

Перейдем к созданию Gateway