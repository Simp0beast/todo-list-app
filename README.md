# Todo List App

Проект реализует полный CI/CD pipeline для приложения todo-list-app.

## Используемые технологии:
```
Docker
Kubernetes (Minikube)
GitHub Actions
Prometheus + Grafana
```
## Структура проекта
```
.
├── k8s/
│   ├── deployment.yaml
│   ├ service.yaml
│   ── ingress.yaml
│   └── monitoring.yaml
├── .github/workflows/
│   └── ci-cd.yaml
├─Dockerfile
├── src/
└── README.md
```
## Развертывание инфраструктуры
```
minikube start
minikube addons enable ingress
```
## Развертывание приложения
```
kubectl apply -f k8s/
```
## Доступ к приложению
```
minikube tunnel
curl --resolve todo.local:80:127.0.0.1 http://todo.local
```
## Доступ к приложению из интернета (ngrok)

Для работы приложения вне локальной сети используется ngrok.

После установки с официального сайта нужно выполнить авторизацию:
```
ngrok config add-authtoken <YOUR_TOKEN>
```
Если приложение доступно локально через Kubernetes:
```
kubectl port-forward svc/todo-list-app 8080:3000
```
Запускаем ngrok:
```
ngrok http 8080
```
ngrok выдаст публичный URL вида:
```
https://abcd1234.ngrok.io
```
По этому адресу приложение будет доступно из интернета.

## CI (Continuous Integration)

При каждом push в ветки develop и main запускается pipeline:

1. сборка Docker-образа
2. тестирование приложения
3. публикация Docker-образа в Docker Hub
4. версионирование образов (по commit SHA и тегу latest)

Пример имени образа:
```
simpotiaga/todo-list-app:<commit-sha>
simpotiaga/todo-list-app:latest
```
## CD (Continuous Delivery)

Деплой приложения выполняется локально, а не из CI, т.к. используется локальный Kubernetes-кластер (Minikube), который работает на машине и недоступен из GitHub Actions.

После успешного pipeline необходимо вручную обновить приложение в Kubernetes:
```
kubectl set image deployment/todo-list-app \
todo-list-app=simpotiaga/todo-list-app:<commit-sha>
```
## Версионирование

Используется:
```
<dockerhub>/todo-list-app:<commit_sha>
```
## Секреты

Хранятся в GitHub Secrets:
```
DOCKER_USERNAME
DOCKER_PASSWORD
KUBECONFIG
```
## Мониторинг

Запуск:
```
kubectl apply -f k8s/monitoring.yaml
```
Доступ:
```
minikube service -n monitoring grafana
```
##  Git Flow

1. main — production
2. develop — разработка
3. feature — новые фичи

## Релизный цикл

1. Разработка → feature branch
2. Merge в develop
3. Тестирование
4. Merge в main
5. Деплой
