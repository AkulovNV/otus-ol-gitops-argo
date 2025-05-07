k apply -f applications/app-in-app/Application.yaml -n argocd
argocd app get rabbitmq-operator

# Если приложение rabbitmq-operator упало с ошибкой
argocd app get rabbitmq-operator --refresh