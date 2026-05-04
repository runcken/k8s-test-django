# Django Site

Докеризированный сайт на Django для экспериментов с Kubernetes.

Внутри контейнера Django приложение запускается с помощью Nginx Unit, не путать с Nginx. Сервер Nginx Unit выполняет сразу две функции: как веб-сервер он раздаёт файлы статики и медиа, а в роли сервера-приложений он запускает Python и Django. Таким образом Nginx Unit заменяет собой связку из двух сервисов Nginx и Gunicorn/uWSGI. [Подробнее про Nginx Unit](https://unit.nginx.org/).

## Как подготовить окружение к локальной разработке

Код в репозитории полностью докеризирован, поэтому для запуска приложения понадобится Docker. Инструкции по его установке на официальных сайтах:

- [Get Started with Docker](https://www.docker.com/get-started/)


## Как запустить сайт для локальной разработки в Minikube

### Запустить Minikube с драйвером Docker

```shell
$ minikube start --driver=docker
```

### Проверить статус

```shell
$ minikube status
$ minikube get nodes
```

### Загрузка с Docker-образа. Перейти в папку с Dockerfile

```shell
$ docker build -t django_app:latest .
```

### Загрузить образ в Minikube

```shell
$ minikube image load django_app:latest
```

### Перейти в папку manifests. Включить ingress

```shell
$ minikube addons enable ingress
```

### Применить манифесты

```shell
$ kubectl apply -f secrets.yaml
$ kubectl apply -f configmap.yaml
$ kubectl apply -f postgres-deployment.yaml
$ kubectl apply -f django-deployment.yaml
$ kubectl apply -f ingress.yaml
$ kubectl apply -f session-cleanup-job.yaml - для очистки сессий пользователей вручную
$ kubectl apply -f session-cleanup-cronjob.yaml - для очистки сесси1 пользователей по расписанию
$ kubectl apply -f migrate-job.yaml - для применения миграций
```

### Создать суперпользователя

```shell
$ kubectl exec -it deployment/django -- python manage.py createsuperuser
```

### Добавить домен в /etc/hosts

```shell
$ echo "$(minikube ip) star-burger.test" | sudo tee -a /etc/hosts
```

Сайт будет доступен по http://star-burger.test

### Проверка статуса всех ресурсов

```shell
$ kubectl get all
```

### Перезапуск Django

```shell
$ kubectl rollout restart deployment/django
```











