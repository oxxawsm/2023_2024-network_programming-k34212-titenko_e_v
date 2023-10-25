University: [ITMO University](https://itmo.ru/ru/)

Faculty: [FICT](https://fict.itmo.ru)

Course: [Network programming](https://github.com/itmo-ict-faculty/network-programming)

Year: 2023/2024

Group: K34212

Author: Titenko Elena Vitalevna

Lab: Lab1

Date of create: 10.09.2023

Date of finished: 25.10.2023

# Установка CHR и Ansible, настройка VPN

**Цель:** развертывание виртуальной машины на базе платформы Yandex Cloud с установленной системой контроля конфигураций Ansible и установка CHR в VirtualBox.

## Ход работы

### Приготовления

1. Развернута виртуальная машина Ubuntu 20.04 на платформе Yandex Cloud
   
2. На виртуальную машину установлены python3 c помощью команды `sudo apt install python3-pip` и Ansible с помощью команды `sudo pip3 install ansible`
   
3. Далее в VirtualBox была создана виртуальная машина CHR – установлена с образа, скачанного с сайта Mikrotik.

![telegram-cloud-photo-size-2-5296670024842465225-x](https://github.com/oxxawsm/2023_2024-network_programming-k34212-titenko_e_v/assets/63160594/321c222d-7385-4a3e-a2ba-8879f1272821)


## Настройка сервера Wireguard

На этом этапе необходимо поднять VPN-сервер для организации VPN-туннеля между сервером автоматизации с Ansible и машиной CHR.

На виртуальной машине в облаке был поднят сервер Wireguard, сгенерированы ключи для сервера и клиента. Информация из конфигурационного файла *wg0-client-wg-client.conf*:
```
[Interface]
PrivateKey = <приватный ключ сервера>
Address = 10.66.66.2/32,fd42:42:42::2/128
DNS = 1.1.1.1, 1.0.0.1

[Peer]
PublicKey = <публичный ключ клиента>
Endpoint = 10.129.0.4:64404
AllowedIPs = 10.66.66.102/32
```
Статус сервера (запущен):

<img width="571" alt="image" src="https://github.com/oxxawsm/2023_2024-network_programming-k34212-titenko_e_v/assets/63160594/6f70eba8-20f9-49d4-b643-6f923b8d6fe5">

## Настройка клиента (peer)

Для успешного поднятия VPN-туннеля необходимо настроить клиента на виртуальной машине CHR. Для этого использовалась программа Winbox - было произведено подключение к виртуальной машине через поиск neighbors (предварительно на машине был выполнен вход в учетную запись с кредами admin admin).

1. В настройках Interface заполнены поля - название интерфейса, mtu оставлен, как указано, listen port, приватный ключ:
<img width="707" alt="image" src="https://github.com/oxxawsm/2023_2024-network_programming-k34212-titenko_e_v/assets/63160594/5650c8bd-57cc-4032-ba32-947a5473d9f6">


2. Во вкладке Peers создан клиент - указан только что созданный интерфейс, публичный ключ сервера, его публичный ip-адрес, порт и сеть адресов, по которым будет идти подключение:

![telegram-cloud-photo-size-2-5316967232829509687-x](https://github.com/oxxawsm/2023_2024-network_programming-k34212-titenko_e_v/assets/63160594/67d669e5-d5af-492f-9ba8-67daadac0b92)

3. Далее в настройках IP -> Adresses необходимо указать выбранную сеть и созданный интерфейс:

<img width="494" alt="image" src="https://github.com/oxxawsm/2023_2024-network_programming-k34212-titenko_e_v/assets/63160594/0c9a1f34-eaad-4431-a902-2c5e0645084a">

4. Далее в настройках IP -> Firewall создано правило NAT Rule Masquerade - оно позволяет преобразовыавть несколько адресов в другой одиночный адрес. Его можно использовать, чтобы скрыть один\несколько адресов во внутренней сети за ip-адресом, который требуется сделать общедоступным:

![telegram-cloud-photo-size-2-5316967232829509704-x](https://github.com/oxxawsm/2023_2024-network_programming-k34212-titenko_e_v/assets/63160594/44ab2407-6a7c-472c-a795-d4e82f6d2349)

## Проверка работоспособности

![telegram-cloud-photo-size-2-5307685224387301723-x](https://github.com/oxxawsm/2023_2024-network_programming-k34212-titenko_e_v/assets/63160594/71b4b3d8-9239-4870-b517-b0066d0fa822)

В результате получилась сеть, которая соответствует следующей схеме:

<img width="571" alt="Снимок экрана 2023-10-25 в 23 41 17" src="https://github.com/oxxawsm/2023_2024-network_programming-k34212-titenko_e_v/assets/63160594/28dd37c3-e9e0-43a3-81ab-c55d68f3e9d8">

## Вывод
В ходе выполнения лабораторной работы была развернута виртуальная машина на базе платформы Yandex Cloud с установленной системой контроля конфигураций Ansible и установка CHR в VirtualBox.
С помощью WireGuard можно создать VPN-туннель между виртуальным устройством с RouterOS и другим виртуальным устройством, расположенным удаленно. Для взаимодействия с RouterOS было удобнее всего использовать графический интерфейс Winbox. 
