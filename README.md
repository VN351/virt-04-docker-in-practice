# Домашнее задание к занятию 5. «Практическое применение Docker»

### Инструкция к выполнению

1. Для выполнения заданий обязательно ознакомьтесь с [инструкцией](https://github.com/netology-code/devops-materials/blob/master/cloudwork.MD) по экономии облачных ресурсов. Это нужно, чтобы не расходовать средства, полученные в результате использования промокода.
3. **Своё решение к задачам оформите в вашем GitHub репозитории.**
4. В личном кабинете отправьте на проверку ссылку на .md-файл в вашем репозитории.
5. Сопроводите ответ необходимыми скриншотами.

---
## Примечание: Ознакомьтесь со схемой виртуального стенда [по ссылке](https://github.com/netology-code/shvirtd-example-python/blob/main/schema.pdf)

---

## Задача 0
1. Убедитесь что у вас НЕ(!) установлен ```docker-compose```, для этого получите следующую ошибку от команды ```docker-compose --version```
```
Command 'docker-compose' not found, but can be installed with:

sudo snap install docker          # version 24.0.5, or
sudo apt  install docker-compose  # version 1.25.0-1

See 'snap info docker' for additional versions.
```
В случае наличия установленного в системе ```docker-compose``` - удалите его.  
2. Убедитесь что у вас УСТАНОВЛЕН ```docker compose```(без тире) версии не менее v2.24.X, для это выполните команду ```docker compose version```  
###  **Своё решение к задачам оформите в вашем GitHub репозитории!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!**

---

## Задача 1
1. Сделайте в своем github пространстве fork [репозитория](https://github.com/netology-code/shvirtd-example-python/blob/main/README.md).
   Примечание: В связи с доработкой кода python приложения. Если вы уверены что задание выполнено вами верно, а код python приложения работает с ошибкой то используйте вместо main.py файл old_main.py(просто измените CMD)
3. Создайте файл с именем ```Dockerfile.python``` для сборки данного проекта(для 3 задания изучите https://docs.docker.com/compose/compose-file/build/ ). Используйте базовый образ ```python:3.9-slim```. 
Обязательно используйте конструкцию ```COPY . .``` в Dockerfile. Не забудьте исключить ненужные в имадже файлы с помощью dockerignore. Протестируйте корректность сборки.  
4. (Необязательная часть, *) Изучите инструкцию в проекте и запустите web-приложение без использования docker в venv. (Mysql БД можно запустить в docker run).
5. (Необязательная часть, *) По образцу предоставленного python кода внесите в него исправление для управления названием используемой таблицы через ENV переменную.

## Ответ к заданию 1
1. ![alt text](https://github.com/VN351/virt-04-docker-in-practice/raw/main/images/task-1-1.png)
2. 
   - Dockerfile.python
      ```
      # Используем базовый образ Python версии 3.9-slim
      FROM python:3.9-slim

      # Устанавливаем рабочую директорию в контейнере
      WORKDIR /app

      # Копируем все файлы из текущей директории в контейнер
      COPY . .

      # Устанавливаем зависимости
      RUN pip install --no-cache-dir -r requirements.txt || true

      # Указываем команду по умолчанию для запуска приложения
      CMD ["python", "main.py"]
      ``` 
   
   - .dockerignore
   
      ```
      # Исключаем git информацию
      .git
      .gitignore

      # Исключаем виртуальное окружение
      venv

      # Исключаем прочие ненужные файлы
      _pycache__
      *.pyc
      *.pyo
      *.pyd
      *.yaml
      not_tested_main.py
      .md
      LICENSE
      .pdf
      ```
   ![alt text](https://github.com/VN351/virt-04-docker-in-practice/raw/main/images/task-1-2.png)      
3. 
   ![alt text](https://github.com/VN351/virt-04-docker-in-practice/raw/main/images/task-1-3.png)
4. Был установлен пакет для взоимодействия c .env - python-dotenv <br>
   Изменения внесенные в начало файла main.py
   ```
   from dotenv import load_dotenv

   load_dotenv()
   ```
   ![alt text](https://github.com/VN351/virt-04-docker-in-practice/raw/main/images/task-1-4.png)


---
### ВНИМАНИЕ!
!!! В процессе последующего выполнения ДЗ НЕ изменяйте содержимое файлов в fork-репозитории! Ваша задача ДОБАВИТЬ 5 файлов: ```Dockerfile.python```, ```compose.yaml```, ```.gitignore```, ```.dockerignore```,```bash-скрипт```. Если вам понадобилось внести иные изменения в проект - вы что-то делаете неверно!
---

## Задача 2 (*)
1. Создайте в yandex cloud container registry с именем "test" с помощью "yc tool" . [Инструкция](https://cloud.yandex.ru/ru/docs/container-registry/quickstart/?from=int-console-help)
2. Настройте аутентификацию вашего локального docker в yandex container registry.
3. Соберите и залейте в него образ с python приложением из задания №1.
4. Просканируйте образ на уязвимости.
5. В качестве ответа приложите отчет сканирования.

## Ответ к заданию 2
   ![alt text](https://github.com/VN351/virt-04-docker-in-practice/raw/main/images/task-2-2.png)
   ![alt text](https://github.com/VN351/virt-04-docker-in-practice/raw/main/images/task-2-3.png)

## Задача 3
1. Изучите файл "proxy.yaml"
2. Создайте в репозитории с проектом файл ```compose.yaml```. С помощью директивы "include" подключите к нему файл "proxy.yaml".
3. Опишите в файле ```compose.yaml``` следующие сервисы: 

- ```web```. Образ приложения должен ИЛИ собираться при запуске compose из файла ```Dockerfile.python``` ИЛИ скачиваться из yandex cloud container registry(из задание №2 со *). Контейнер должен работать в bridge-сети с названием ```backend``` и иметь фиксированный ipv4-адрес ```172.20.0.5```. Сервис должен всегда перезапускаться в случае ошибок.
Передайте необходимые ENV-переменные для подключения к Mysql базе данных по сетевому имени сервиса ```web``` 

- ```db```. image=mysql:8. Контейнер должен работать в bridge-сети с названием ```backend``` и иметь фиксированный ipv4-адрес ```172.20.0.10```. Явно перезапуск сервиса в случае ошибок. Передайте необходимые ENV-переменные для создания: пароля root пользователя, создания базы данных, пользователя и пароля для web-приложения.Обязательно используйте уже существующий .env file для назначения секретных ENV-переменных!

2. Запустите проект локально с помощью docker compose , добейтесь его стабильной работы: команда ```curl -L http://127.0.0.1:8090``` должна возвращать в качестве ответа время и локальный IP-адрес. Если сервисы не стартуют воспользуйтесь командами: ```docker ps -a ``` и ```docker logs <container_name>``` . Если вместо IP-адреса вы получаете ```NULL``` --убедитесь, что вы шлете запрос на порт ```8090```, а не 5000.

5. Подключитесь к БД mysql с помощью команды ```docker exec -ti <имя_контейнера> mysql -uroot -p<пароль root-пользователя>```(обратите внимание что между ключем -u и логином root нет пробела. это важно!!! тоже самое с паролем) . Введите последовательно команды (не забываем в конце символ ; ): ```show databases; use <имя вашей базы данных(по-умолчанию example)>;  SELECT * from requests LIMIT 10;```.

6. Остановите проект. В качестве ответа приложите скриншот sql-запроса.

## Ответ к заданию 3
   ![alt text](https://github.com/VN351/virt-04-docker-in-practice/raw/main/images/task-3-1.png)

## Задача 4
1. Запустите в Yandex Cloud ВМ (вам хватит 2 Гб Ram).
2. Подключитесь к Вм по ssh и установите docker.
3. Напишите bash-скрипт, который скачает ваш fork-репозиторий в каталог /opt и запустит проект целиком.
4. Зайдите на сайт проверки http подключений, например(или аналогичный): ```https://check-host.net/check-http``` и запустите проверку вашего сервиса ```http://<внешний_IP-адрес_вашей_ВМ>:8090```. Таким образом трафик будет направлен в ingress-proxy. ПРИМЕЧАНИЕ:  приложение(old_main.py) весьма вероятно упадет под нагрузкой, но успеет обработать часть запросов - этого достаточно. Обновленная версия (main.py) не прошла достаточного тестирования временем, но должна справиться с нагрузкой.
5. (Необязательная часть) Дополнительно настройте remote ssh context к вашему серверу. Отобразите список контекстов и результат удаленного выполнения ```docker ps -a```
6. В качестве ответа повторите  sql-запрос и приложите скриншот с данного сервера, bash-скрипт и ссылку на fork-репозиторий.

## Ответ к заданию 4
   - bash script
      ```
      REPO_URL="https://github.com/VN351/shvirtd-example-python.git" # URL репозитория
      TARGET_DIR="/opt/project"                                      # Каталог для загрузки репозитория
      BRANCH="main"                                                  # Ветка репозитория


      # Проверка прав суперпользователя
      if [ "$EUID" -ne 0 ]; then
         echo "Пожалуйста, запустите скрипт с правами суперпользователя (sudo)."
         exit 1
      fi

      # Клонирование репозитория
      echo "Клонируем репозиторий $REPO_URL в $TARGET_DIR..."
      rm -rf "$TARGET_DIR" # Удаляем старую версию, если она существует
      git clone --branch "$BRANCH" "$REPO_URL" "$TARGET_DIR" || {
         echo "Ошибка при клонировании репозитория."
         exit 1
      }

      # Переход в каталог проекта
      cd "$TARGET_DIR" || {
         echo "Не удалось перейти в каталог $TARGET_DIR."
         exit 1
      }

      # Запуск проекта
      echo "Запускаем проект..."

      # Запуск Docker Compose с указанным файлом
         docker compose up --build || {
            echo "Ошибка при запуске Docker Compose."
            exit 1
      }

      else
         echo "Не удалось определить способ запуска проекта. Проверьте документацию вашего репозитория."
         exit 1
      fi
      ```
   [Ссылка на GitHub с приложением](https://github.com/VN351/shvirtd-example-python.git)
   ![alt text](https://github.com/VN351/virt-04-docker-in-practice/raw/main/images/task-4-1.png)
   ![alt text](https://github.com/VN351/virt-04-docker-in-practice/raw/main/images/task-4-2.png)
   ![alt text](https://github.com/VN351/virt-04-docker-in-practice/raw/main/images/task-4-3.png)

## Задача 5 (*)
1. Напишите и задеплойте на вашу облачную ВМ bash скрипт, который произведет резервное копирование БД mysql в директорию "/opt/backup" с помощью запуска в сети "backend" контейнера из образа ```schnitzler/mysqldump``` при помощи ```docker run ...``` команды. Подсказка: "документация образа."
2. Протестируйте ручной запуск
3. Настройте выполнение скрипта раз в 1 минуту через cron, crontab или systemctl timer. Придумайте способ не светить логин/пароль в git!!
4. Предоставьте скрипт, cron-task и скриншот с несколькими резервными копиями в "/opt/backup"

## Задача 6
Скачайте docker образ ```hashicorp/terraform:latest``` и скопируйте бинарный файл ```/bin/terraform``` на свою локальную машину, используя dive и docker save.
Предоставьте скриншоты  действий .

## Ответ к заданию 6
![alt text](https://github.com/VN351/virt-04-docker-in-practice/raw/main/images/task-6-1.png)
![alt text](https://github.com/VN351/virt-04-docker-in-practice/raw/main/images/task-6-2.png)
![alt text](https://github.com/VN351/virt-04-docker-in-practice/raw/main/images/task-6-3.png)

## Задача 6.1
Добейтесь аналогичного результата, используя docker cp.  
Предоставьте скриншоты  действий .

## Ответ к заданию 6.1
![alt text](https://github.com/VN351/virt-04-docker-in-practice/raw/main/images/task-6-4.png)

## Задача 6.2 (**)
Предложите способ извлечь файл из контейнера, используя только команду docker build и любой Dockerfile.  
Предоставьте скриншоты  действий .

## Задача 7 (***)
Запустите ваше python-приложение с помощью runC, не используя docker или containerd.  
Предоставьте скриншоты  действий .
