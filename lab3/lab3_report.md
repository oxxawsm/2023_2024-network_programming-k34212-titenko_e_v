University: ITMO University

Faculty: FICT

Course: Network programming

Year: 2023/2024

Group: K34212

Author: Titenko Elena Vitalevna

Lab: Lab3

Date of create: 25.11.2023

Date of finished: 29.11.2023

# Развертывание Netbox, сеть связи как источник правды в системе технического учета Netbox

**Цель:** С помощью Ansible и Netbox собрать всю возможную информацию об устройствах и сохранить их в отдельном файле.

## Ход работы

# Установка и настройка Netbox

Установлен и настроен postgreSQL с помощью команды `sudo apt install postgresql libpq-dev -y`

![image](https://github.com/oxxawsm/2023_2024-network_programming-k34212-titenko_e_v/assets/63160594/d381af68-27be-48b2-9c3a-39de0ab054c2)

Далее была создана новая база данных netbox, а также user с максимальными правами на управление бд:

![image](https://github.com/oxxawsm/2023_2024-network_programming-k34212-titenko_e_v/assets/63160594/c4b81f67-832e-477f-be5e-b69325370b53)

![image](https://github.com/oxxawsm/2023_2024-network_programming-k34212-titenko_e_v/assets/63160594/aa8a1162-8414-46c6-886f-daf0f94f96d7)

Далее установлен Redis - хранилище значений ключей в памяти, которое netbox использует для кэширования и постановки в очередь: `sudo apt install -y redis-server`

![image](https://github.com/oxxawsm/2023_2024-network_programming-k34212-titenko_e_v/assets/63160594/74144ec7-560b-4b9e-aec0-b81ed8e426d1)

Установлены все нужные для дальнейшей работы пакеты:

`sudo apt install python3 python3-pip python3-venv python3-dev build-essential libxml2-dev libxslt1-dev libffi-dev libpq-dev libssl-dev zlib1g-dev git -y`

Обновлён `pip` до последней версии:

`sudo pip3 install --upgrade pip`

Создана директория, куда будет клонирован репозиторий netbox:

![image](https://github.com/oxxawsm/2023_2024-network_programming-k34212-titenko_e_v/assets/63160594/3a4cebb5-bde4-42da-810a-9c717d406b43)

Склонирован репозиторий:

![image](https://github.com/oxxawsm/2023_2024-network_programming-k34212-titenko_e_v/assets/63160594/33bafe83-0866-4146-8f32-7bb06714d1c7)

Создан системный пользователь netbox с правами на все файлы:

![image](https://github.com/oxxawsm/2023_2024-network_programming-k34212-titenko_e_v/assets/63160594/d1b37aa3-5822-4492-961e-a12a2d782bc7)

Скопирован `configuration.example.py`, который будет использоваться для дальнейшей настройки и сгенерирован секретный ключ:

![image](https://github.com/oxxawsm/2023_2024-network_programming-k34212-titenko_e_v/assets/63160594/5e6e8de4-a59b-4c5a-9f75-fc27e60de783)

Файл `configuration.py` отредактирован следующим образом:

![image](https://github.com/oxxawsm/2023_2024-network_programming-k34212-titenko_e_v/assets/63160594/115e426f-dda6-4f36-bc31-680a5c30ec3d)

![image](https://github.com/oxxawsm/2023_2024-network_programming-k34212-titenko_e_v/assets/63160594/ff3982f6-4c8a-4ea3-b81e-99e19b51159e)

Запущен upgrade скрипт с помощью команды `sudo /opt/netbox/upgrade.sh`

![image](https://github.com/oxxawsm/2023_2024-network_programming-k34212-titenko_e_v/assets/63160594/fa8bbfab-227f-4cb8-b7a4-62e397aadd24)

Выполнен вход в виртуальную среду: `source /opt/netbox/venv/bin/activate`

Создан аккаунт супер-юзера:

![image](https://github.com/oxxawsm/2023_2024-network_programming-k34212-titenko_e_v/assets/63160594/baad2b6d-a87c-413f-bca9-a078d70c3c8b)

Для запуска netbox в режиме daemon были сконфигурированы nginx и gunicorn.

Настройка gunicorn:

![image](https://github.com/oxxawsm/2023_2024-network_programming-k34212-titenko_e_v/assets/63160594/d95b7c87-badd-43ba-9e57-6702b2a9f534)

Настройка Nginx Web Server:
- Установка: `sudo apt install -y nginx`
- Копирование Netbox Nginx конфигурации: `sudo cp /opt/netbox/contrib/nginx.conf /etc/nginx/sites-available/netbox`
- Файл netbox отредактирован так:

  ![image](https://github.com/oxxawsm/2023_2024-network_programming-k34212-titenko_e_v/assets/63160594/7615fb3c-330f-4c4e-8c08-16126a5e9112)
  
- Удаление дефолтной конфигурации: `sudo rm /etc/nginx/sites-enabled/default`
- Перезагрузка nginx: `sudo systemctl restart nginx`

Перезапущены сервисы и добавлены в автозагрузку. NetBox и nginx работают:
![image](https://github.com/oxxawsm/2023_2024-network_programming-k34212-titenko_e_v/assets/63160594/4564ad77-7ab4-4f47-9710-4cc5ed2917c0)

![image](https://github.com/oxxawsm/2023_2024-network_programming-k34212-titenko_e_v/assets/63160594/ae0fd37a-08a0-4ea8-88ce-f821235771bb)

Теперь графический интерфейс netbox можно открыть в браузере:

![image](https://github.com/oxxawsm/2023_2024-network_programming-k34212-titenko_e_v/assets/63160594/6072d5cb-b685-4d1a-82e0-a713ed11a15f)

# Создание роутеров в Netbox

Сведения о виртуальных роутерах из предыдущих работ были внесены в раздел Virtual Machines:

![image](https://github.com/oxxawsm/2023_2024-network_programming-k34212-titenko_e_v/assets/63160594/86662e1a-e438-41d7-a13d-a2f3a634f985)

# Работа с ansible 

Был создан inventory файл, который получает данные о роутерах из Netbox. Подключение производилось с помощью сгенерированного в Netbox токена.

![image](https://github.com/oxxawsm/2023_2024-network_programming-k34212-titenko_e_v/assets/63160594/28973046-558b-44e6-b946-2c7cf4d11917)

Далее написан файл playbook для получения данных:

![image](https://github.com/oxxawsm/2023_2024-network_programming-k34212-titenko_e_v/assets/63160594/dd994d2e-32b5-4c56-89ea-0f0680ceb66f)

Файл выполнен корректно:

![image](https://github.com/oxxawsm/2023_2024-network_programming-k34212-titenko_e_v/assets/63160594/00ef22d5-de3b-4299-bf74-e34c2eeebf5c)

Результат:

![image](https://github.com/oxxawsm/2023_2024-network_programming-k34212-titenko_e_v/assets/63160594/8e01df63-cfdf-4bae-98a6-0c22403cb136)

Далее написан еще один playbook, который изменяет в netbox названия виртуальных машин роутеров.
![image](https://github.com/oxxawsm/2023_2024-network_programming-k34212-titenko_e_v/assets/63160594/9e5d0fb2-8e31-4435-9359-cd569eb613d0)

Playbook успешно выполнился, на роутерах появился новый интерфейс, поменялось имя.

![image](https://github.com/oxxawsm/2023_2024-network_programming-k34212-titenko_e_v/assets/63160594/6d0dd8be-6fb7-4e21-a013-8e5cde22c46f)

![image](https://github.com/oxxawsm/2023_2024-network_programming-k34212-titenko_e_v/assets/63160594/60390adf-cabd-4545-bdcf-7ca48068fc98)

Далее по заданию нужно получить серийный номер устройства и внести эту информацию в Netbox. Но для вирутальных устройств серийный номер не выдается, поэтому были выбраны сведения об архитектуре устройства для занесения новой информации - необходимо получать эти данные, и потом передавать значение в переменной в кастомное поле в конфигурации роутера в netbox. 
Для этого написан playbook:

![image](https://github.com/oxxawsm/2023_2024-network_programming-k34212-titenko_e_v/assets/63160594/ed60f0ea-a616-4329-9625-e0473d0fffb2)

![image](https://github.com/oxxawsm/2023_2024-network_programming-k34212-titenko_e_v/assets/63160594/d5eee77f-2d61-4ef5-989f-79d23eed88ca)

Операция выполнена успешно - в логах есть сообщения об обновлении:

![image](https://github.com/oxxawsm/2023_2024-network_programming-k34212-titenko_e_v/assets/63160594/8ef2c7c8-b128-4bee-8389-382bb0a8ea6e)

![image](https://github.com/oxxawsm/2023_2024-network_programming-k34212-titenko_e_v/assets/63160594/99006e17-f69e-4c3a-9e6e-283716fca83b)

## Вывод
В ходе лабораторной работы с помощью Ansible и Netbox собрана вся возможная информация об устройствах. Было выяснено на практике, что netbox довольно удобный инструмент для документирования сетей.
