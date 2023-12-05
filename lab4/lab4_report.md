University: ITMO University

Faculty: FICT

Course: Network programming

Year: 2023/2024

Group: K34212

Author: Titenko Elena Vitalevna

Lab: Lab4

Date of create: 29.11.2023

Date of finished: 3.12.2023

# Базовая 'коммутация' и туннелирование с использованием языка программирования P4

**Цель:** изучить синтаксис языка программирования P4 и выполнить два обучающих задания от Open network foundation для ознакомления на практике с P4.

## Ход работы

### Подготовка среды

На рабочем компьютере установлены VirtualBox и Vagrant:

![image](https://github.com/oxxawsm/2023_2024-network_programming-k34212-titenko_e_v/assets/63160594/1055d814-af1b-4f6f-9944-46f9cd647ea8)

 Склонирован репозиторий p4lang tutorials:

 ![image](https://github.com/oxxawsm/2023_2024-network_programming-k34212-titenko_e_v/assets/63160594/322cbdbf-8fa9-4bf2-b504-f1b2922779d6)

Далее запущен vagrant (для завершения установки пришлось подождать около часа):

![image](https://github.com/oxxawsm/2023_2024-network_programming-k34212-titenko_e_v/assets/63160594/03547d70-c7f0-4074-a831-035c633490f0)

После установки в VirtualBox видим виртуальную машину с двумя аккаунтами P4 и Vagrant:

![image](https://github.com/oxxawsm/2023_2024-network_programming-k34212-titenko_e_v/assets/63160594/d1839d7d-ae64-46e7-90ac-4c76b26c6e36)

### basic

В задании нужно настроить переадресацию на switch на такой топологии:

![image](https://github.com/oxxawsm/2023_2024-network_programming-k34212-titenko_e_v/assets/63160594/3a89cf00-ffc2-451e-aa9e-d558d0e60dff)

Топология сети описана в json:

![image](https://github.com/oxxawsm/2023_2024-network_programming-k34212-titenko_e_v/assets/63160594/18808dd5-aca4-4f05-842d-38b85099cf79)

Также для каждого свитча есть правила, также в формате json (например, switch 1):

![image](https://github.com/oxxawsm/2023_2024-network_programming-k34212-titenko_e_v/assets/63160594/649ec2b2-735c-4151-9157-5baf4d0cf3b0)

Запуск mininet в папке задания:

![image](https://github.com/oxxawsm/2023_2024-network_programming-k34212-titenko_e_v/assets/63160594/6075a6ab-b3fb-4ecf-a4dc-229f30527fb2)

Проверка несвязности устройств с помощью ping:

![image](https://github.com/oxxawsm/2023_2024-network_programming-k34212-titenko_e_v/assets/63160594/dd3f000b-88e1-40f8-aac0-a907af9ce88e)

Теперь можно перейти к исправлению файла basic.p4, где и содержится вся основная логика. Сперва определяются типы и структуры данных (HEADERS):

![image](https://github.com/oxxawsm/2023_2024-network_programming-k34212-titenko_e_v/assets/63160594/9eb3d337-f73f-498a-badc-2dd8eb24a09b)

Далее описан парсер, который должен переводить заголовки из входящего пакета в вышеописанную структуру.

Для этого из пакета вместе с содержимым берём заголовок *ethernet* -> смотрим, соответствует ли поле *etherType* номеру протокола *ipv4* -> если да, то берем и заголовок *ipv4*^:

![image](https://github.com/oxxawsm/2023_2024-network_programming-k34212-titenko_e_v/assets/63160594/d7d0af4d-273e-44bb-a17c-683ca73b04a4)

Далее было необходимо дописать логику для входящих пакетов и объединения их в пакет. 

Для этого совершаются проверки *ipv4* -> далее адрес назначения пакета соотносится с таблицей *ipv4_lpm* -> если совпадают, то выполняется действие, которое указано в правилах. *ipv4_forward* - это действие переадресации, которое указывает порт для выхода, изменяет mac-address источника на тот, что был указан в пакете как адрес назначения, вместе с этим меняется адрес назначения на адрес, взятый из таблицы. ttl пакета уменьшается на 1.

![image](https://github.com/oxxawsm/2023_2024-network_programming-k34212-titenko_e_v/assets/63160594/32d247f1-3522-4e52-8d52-230c82bb9065)

В deparser происходит упаковка заголовков:

![image](https://github.com/oxxawsm/2023_2024-network_programming-k34212-titenko_e_v/assets/63160594/461c893c-b37c-41a4-89c1-c6201de1c702)

Для проверки корректности кода снова запущен mininet. Проверка связности:

![image](https://github.com/oxxawsm/2023_2024-network_programming-k34212-titenko_e_v/assets/63160594/857c8688-b899-4dcf-a3dc-58a98eea41f8)

### basic tunnel

В этом задании нужно добавить заголовок инкапсуляции *ipv4*.

Топология следующая:

![image](https://github.com/oxxawsm/2023_2024-network_programming-k34212-titenko_e_v/assets/63160594/37ff0d02-a38a-4aa0-96b1-2c2dc0ece95e)

![image](https://github.com/oxxawsm/2023_2024-network_programming-k34212-titenko_e_v/assets/63160594/c13ea3c7-efb0-4e1e-83de-488248db70e5)

Здесь необходимо добавить проверку соответствия *etherType* и *myTunnel*. Если соответствие есть, то распаковываем *myTunnel*, если *ipv4* инкапсулирвоан в *myTunnel*, то распакавываем и его:

![image](https://github.com/oxxawsm/2023_2024-network_programming-k34212-titenko_e_v/assets/63160594/714f0b28-8ff7-4c5c-ab7e-81fbde2c058b)

Далее, если присутствует заголовок *myTunnel*, то обрабатываем пакеты согласно правилам, описанным в *myTunnel_exact*. Если заголовка *myTunnel* нет, но есть *ipv4*, то используем правила из *ipv4_lpm*.

Здесь также добавляется действие *myTunnel_forward*, сипользующееся для пересылки - указываем порт направления пакета из правил.

![image](https://github.com/oxxawsm/2023_2024-network_programming-k34212-titenko_e_v/assets/63160594/d89689d8-94ff-4173-a68d-e48b34c3f795)

В deparser происходит упаковка заголовка *myTunnel*:

![image](https://github.com/oxxawsm/2023_2024-network_programming-k34212-titenko_e_v/assets/63160594/60cdc1d5-36da-4570-96a2-067602676ad7)

Далее проверим связность.

В mininet запущены терминалы h1 и h2:

![image](https://github.com/oxxawsm/2023_2024-network_programming-k34212-titenko_e_v/assets/63160594/2f30ae0b-5b78-4ee8-9ab9-424b47557ddd)

В h2 запущен получатель и с h1 отправлен пакет. Пакет дошёл

![image](https://github.com/oxxawsm/2023_2024-network_programming-k34212-titenko_e_v/assets/63160594/ad8345ed-6cb4-475b-b074-087f6087289e)

Далее для проверки тоннеля запущен ещё один пакет с *myTunnel*. Пакет дошёл

![image](https://github.com/oxxawsm/2023_2024-network_programming-k34212-titenko_e_v/assets/63160594/504eaa14-8270-4c21-93dd-b01c8e7d50f5)

Теперь с h1 запущен пакет, который должен прийти в h3 и через *myTunnel* прийти в h2:

![image](https://github.com/oxxawsm/2023_2024-network_programming-k34212-titenko_e_v/assets/63160594/271ff115-1fed-45fe-894d-179db59270e8)

Как видно, пакет дошёл до h2 - *ipv4* не сработал.

## Вывод

В ходе выполнения лабораторной работы были выполнены два ознакомительных задания для понимания работы языка p4 и настройки устройств в помощью него.
