# Todo List App

Проект реализует полный CI/CD pipeline для приложения todo-list-app с деплоем в Kubernetes.

## Используемые технологии:

Docker
Kubernetes (Minikube)
GitHub Actions
Prometheus + Grafana

## Структура проекта
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

## Развертывание инфраструктуры

minikube start
minikube addons enable ingress

## Развертывание приложения

kubectl apply -f k8s/

## Доступ к приложению

minikube tunnel
curl --resolve todo.local:80:127.0.0.1 http://todo.local

## Доступ к приложению из интернета (ngrok)

Для работы приложения вне локальной сети используется ngrok.

После установки с официального сайта нужно выполнить авторизацию:

ngrok config add-authtoken <YOUR_TOKEN>

Если приложение доступно локально через Kubernetes:

kubectl port-forward svc/todo-list-app 8080:3000

Запускаем ngrok:

ngrok http 8080

ngrok выдаст публичный URL вида:

https://abcd1234.ngrok.io

По этому адресу приложение будет доступно из интернета.

## CI/CD

Pipeline выполняет:

Установку зависимостей
Запуск тестов
Сборку Docker-образа
Публикацию в DockerHub
Деплой в Kubernetes

## Версионирование

Используется:

<dockerhub>/todo-list-app:<commit_sha>

## Секреты

Хранятся в GitHub Secrets:

DOCKER_USERNAME
DOCKER_PASSWORD
KUBECONFIG

## Мониторинг

Запуск:

kubectl apply -f k8s/monitoring.yaml

Доступ:

minikube service -n monitoring grafana

##  Git Flow
main — production
develop — разработка
feature — новые фичи

## Релизный цикл
Разработка → feature branch
Merge в develop
Тестирование
Merge в main
Автодеплой
