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
