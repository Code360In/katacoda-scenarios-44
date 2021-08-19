На данном шаге мы создадим пространство имен (namespase), где будут развернуты поды сервисов, а также настроим авто-внедрение контейнера с envoy-прокси в создаваемые поды.

## Создание пространство имен

Для создание пространства имен выполним команду: `kubectl create namespace dev-service-mesh`{{execute}}

Получим список всех существующих пространств имен (иначе называют — виртуальные кластеры) в нашем кластере и убедимся что dev-service-mesh есть в списке: `kubectl get namespace`{{execute}}

## Конфигурация пространства имен

`kubectl config set-context --current --namespace=dev-service-mesh`{{execute}}

`kubectl config view --minify | grep namespace:`{{execute}}

`kubectl label namespace dev-service-mesh istio-injection=enabled`{{execute}}

`kubectl get pods --all-namespaces `{{execute}}