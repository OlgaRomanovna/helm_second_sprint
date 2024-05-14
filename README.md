# helm_second_sprint
  - Клонируем указанный репозиторий тестового приложения. У нас это папка: django-pg-docker-tutorial
  - Дорабатываем приложение - выносим секретные данные по подключению к БД в папку "private_data" и вносим её 
  в гитигнор. Для примера, как выглядит этот файл - private.var.example. Исправляем ошибки.
  - Делаем исполняемыми скрипты в папке scripts:
  ```
  chmod +x test_deploy_app.sh
  chmod +x test_destroy_app.sh
  ```
  - тестово разворачиваем приложение на ноде srv в docker для дебагинга:
  ```
  cd /django-pg-docker-tutorial-clone && /scripts/test_deploy_app.sh
  ```
  Видим удачное тестовое разворачивание:

https://disk.yandex.ru/d/mmZy46yJk2pjWQ

https://disk.yandex.ru/d/Lc1w1XB_nU0XdQ
  - Так как приложение работает, то удаляем тестовое разворачивание приложения в docker:
  ```
  cd /django-pg-docker-tutorial-clone && /opt/CI-CD/scripts/test_destroy_app.sh
  ```
  - В рамках подзадачи в роли Docker registry будем использовать Dockerhub. Логинимся в Dockerhub.
  ```
  docker login -u oromanovna
  ```
  - Cоздаёми там репозиторий для хранения артифакта сборки. В нашем случае это: mikhailrizhkin1/diplom
 https://disk.yandex.ru/d/F5ZZ6ayLVjLWzw
  
  - В качестве CI/CD будем использовать Gitlab-CI
  - Создаём репозиторий в gitlab.com под проект: https://gitlab.com/devops7953916/helm_second_sprint
  - Создаём в нём гитлаб-раннер по пути Settings-CI/CD-Runners и отключаем шарэд раннеры

  - На сервере srv, настраиваем Gitlab-Runner:
  ```
  gitlab-runner register --url https://gitlab.com --token <your_token>
  ```
  И если видим его зелённым цветом, то всё в порядке.
https://disk.yandex.ru/d/Zy0rMdfFaDRnuQ

  - Вносим пользователей в разрешение на docker
  ```
  usermod -aG docker ubuntu
  usermod -aG docker root
  usermod -aG docker gitlab-runner
  ```
  - Создаём нужные нам переменные для хранения приватных данных и другой информации по пути Settings-CI/CD-Variables:
https://disk.yandex.ru/d/RQ-NG1cAo6TzZQ

- Описываем pipeline в стандартном .gitlab-ci.yml файле состоящий из двух стадий - сборки приложения из докерфайла и выкатка - деплой приложения. Запускаем пробно pipeline, который собирает проект и пушит его как артефакт в докер хаб. Результат тестовой работы pipeline: 
https://disk.yandex.ru/d/PFlgYI7jdz4HWQ
https://disk.yandex.ru/d/atFn61p5wdl7jQ
- Для разворачивания приложения в кластера kubernetes пишем манифесты в каталоге "Kube-manifests".
- Секретные данные кодируем и помещаем в манифест "credentials.yaml":
```
echo -n 'what_we_want_to_encode' | base64
```

- Для приложения создадим отдельный нэймспейс, куда будем его диплоить:
```
kubectl create namespace diplom
```
- Переходим в каталог с манифестами и деплоим приложение в ранее развёрнутый K8S кластер:
```
kubectl apply -f . -n diplom 
```
https://disk.yandex.ru/d/ptxI5VpSTUzi0Q
https://disk.yandex.ru/d/r56-QwZFvC9G1g
https://disk.yandex.ru/d/ZhTmlv3RDS8lTg

- Для удаления приложения с кластера переходим в каталог с манифестами:
```
kubectl delete -f . -n diplom 
```

Подзадача 2: Описываем приложение в Helm-чарт.

- На базе нашего приложения м манифестов создаём Helm chart. Переходим в папку с манифестами и формируем свой чарт:
```
helm create app-dpdt
tree app-dpdt
```
https://disk.yandex.ru/d/EIa7PKdr3l4sXg

- Развернём наш Helm chart в кластере k8s. Переходим в папку с c чартом и запускаем установку с учетом данных в credentials.yaml:
```
helm upgrade --install -n diplom --values templates/credentials.yaml --set service.type=NodePort app-dpdt .
```
Подзадача 3: Описываем стадию деплоя в Helm

- Архивируем созданный helm-chart из папки выше чарта:
```
helm package ./app-dpdt
```
- Копируем и пушим созданный архив и сам чарт в репозиторий GitLab c CI/CD
- Разрабатываем второй стейдж ci для развёртывания приложения в кластере k8s новой версии на основании изменения тэга То есть у нас тригер будет - тэг.
https://disk.yandex.ru/d/GFG-WhWPHH_MUw
