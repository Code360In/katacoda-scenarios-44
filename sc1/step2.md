На этом шаге мы установим Istio

## Task

Давайте загрузим istioctl - утилиту для конфигурации Istio выполнив команду: `curl -sL https://istio.io/downloadIstioctl | sh -`

Для того чтоботы работать с istioctl из командной строки давайте экспортируем путь к ней при помощи команды: `export PATH=$PATH:$HOME/.istioctl/bin`

Проверим готовность среды для установки Istio: `istioctl x precheck`

Запустим установку Istio: `istioctl install`