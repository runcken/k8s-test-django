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

### Загрузка с Docker-образа. Перейти в папку с Dockerfilesession-cleanup-job.yaml
kubectl apply -f session-cleanup-job.yaml - вручную

sesion-cleanup-cronjob.yaml
kubectl apply -f session-cleanup-cronjob.yaml - авто

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











222222
создать k8s-manifests

secrets.yaml
configmap.yaml
secrets.example.yaml

применить 
kubectl apply -f secrets.yaml
kubectl apply -f configmap.yaml
kubectl apply -f postgres-deployment.yaml
kubectl apply -f django-deployment.yaml


история

kubectl roolout history deployment/django

откат

kubectl rollout undo deployment/django

применить изменения

kubectl apply -f configmap.yaml
kubectl rollout restart deployment/django


ingress

в configmap.yaml
отключить debug
добавить allowed hosts star-burger.test
применить изменения

kubectl apply -f configmap.yaml
kubectl rollout restart deployment/django

minikube addons enable ingress
kubectl get pods -n ingress-nginx

django-deployment.yaml
NodePort -> ClusterIP

применить

создать и применить
kubectl apply -f ingress.yaml
kubectl get ingress
 
добавить в etc/hosts

echo "$(minikube ip) star-burger.test" | sudo tee -a /etc/hosts

clear sessions


session-cleanup-job.yaml
kubectl apply -f session-cleanup-job.yaml - вручную

sesion-cleanup-cronjob.yaml
kubectl apply -f session-cleanup-cronjob.yaml - авто












