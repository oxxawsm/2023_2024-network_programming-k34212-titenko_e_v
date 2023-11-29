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

