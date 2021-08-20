На этом шаге мы установим Istio

## Задача

Давайте загрузим istioctl - утилиту для конфигурации Istio выполнив команду: `curl -sL https://istio.io/downloadIstioctl | sh -`{{execute}}

Для того что-бы работать с istioctl из командной строки давайте экспортируем путь к ней при помощи команды: `export PATH=$PATH:$HOME/.istioctl/bin`{{execute}}

Проверим готовность среды для установки Istio: `istioctl x precheck`{{execute}}

Запустим установку Istio: `istioctl install --set meshConfig.accessLogFile=/dev/stdout --set meshConfig.outboundTrafficPolicy.mode=REGISTRY_ONLY`{{execute}}
Обратите внимание на параметры, применяемые в данной команде:
1) meshConfig.accessLogFile - установив флаг активации записи логов доступа Envoy
2) meshConfig.outboundTrafficPolicy.mode ... https://istio.io/latest/docs/tasks/traffic-management/egress/egress-control/#controlled-access-to-external-services   Запретим исходящий трафик...

Во время установки слудет подвердить намерение указав в терминале символ `"y"`