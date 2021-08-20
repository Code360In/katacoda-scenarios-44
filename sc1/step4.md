На этом и следующих шагах мы развернем бизнес сервис в service mesh и откроем к нему доступ из вне.

Схема создаваемой конфигурации сети:

![Схема service mesh](./assets/scheme1-b.png)

Мы применим манифест Deploymnet на основе чего будет запущен под, содержащий бизнес-сервис.

Давайте рассмотрим этот манифест:

`https://raw.githubusercontent.com/avsinsight/katacoda-scenarios/main/sc1/src/service-b-deployment.yml`{{copy}}

Обратите внимание на ключ spec.template.spec.containers[0].image, значение которого содержит ссылку на image с бизнес сервисом ServiceB из DockerHub,
ключ spec.template.spec.containers[0].ports[0].containerPort (8082), содержащий номер порта который будет открыт у создаваемого контейнера и ключ metadata.labels.app, значение которго будет использовано в следующем манифесте.

ServiceB - это веб-приложение на базе Spring Boot и Java, которое принимает GET запросы по адресу http://localhost:8082/ и возвращает константный ответ вида: `Hello from ServiceB!`

Исходный код приложения: `https://github.com/avsinsight/katacoda-scenarios/tree/main/apps/ServiceB`{{copy}}

Давайте применим манифест, выполнив команду:

`kubectl apply -f https://raw.githubusercontent.com/avsinsight/katacoda-scenarios/main/sc1/src/service-b-deployment.yml`{{execute}}

Проверим статус созданного пода:

`kubectl get pods`{{execute}}

Дождемся состояния пода вида:
`NAME                                    READY   STATUS    RESTARTS   AGE`
`service-b-deployment-786dc4d5b4-h8lkm   2/2     Running   0          14s`

Запишем имя созданного пода в переменную POD_NAME:

`export POD_NAME=$(kubectl get pod -l app=service-b-app -o jsonpath="{.items[0].metadata.name}")`{{execute}}

Рассмотрим логи приложения ServiceB, выполнив команду:

`kubectl logs $POD_NAME`{{execute}}

Рассмотрим конфигурацию и состояние пода более подробно, выполнив команду:

`kubectl describe pod $POD_NAME`{{execute}}

Перейдем к следующему шагу.