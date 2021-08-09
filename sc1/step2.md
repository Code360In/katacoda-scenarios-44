На этом шаге мы установим Istio

## Задача

Давайте загрузим istioctl - утилиту для конфигурации Istio выполнив команду: `curl -sL https://istio.io/downloadIstioctl | sh -`{{execute}}

Для того что-бы работать с istioctl из командной строки давайте экспортируем путь к ней при помощи команды: `export PATH=$PATH:$HOME/.istioctl/bin`{{execute}}

Проверим готовность среды для установки Istio: `istioctl x precheck`{{execute}}

Запустим установку Istio: `istioctl install`{{execute}}