University: ITMO University

Faculty: FICT

Course: Network programming

Year: 2023/2024

Group: K34212

Author: Titenko Elena Vitalevna

Lab: Lab2

Date of create: 11.11.2023

Date of finished: 23.11.2023


# Развертывание дополнительного CHR, первый сценарий Ansible

**Цель:** c помощью Ansible настроить несколько сетевых устройств и собрать информацию о них. Правильно собрать файл Inventory.

## Ход работы

### Установка и настройка второго CHR

   1. Первым шагом была аналогичная первой работе установка второго CHR на VirtualBox через .vdi образ

   2. Далее для настройки еще одного VPN клиента на виртуальной машине с сервером была сгенерирована дополнительная пара ключей с помощью команды:

   ```wg genkey | sudo tee /etc/wireguard/wg0-client2-private.key | wg pubkey | sudo tee /etc/wireguard/wg0-client2-public.key```

   3. На сервере отредактирован файл конфига /etc/wireguard/wg0.conf:
   ```
      [Interface]
      Address = 10.0.0.1/24
      ListenPort = 51820
      PrivateKey = [приватный ключ сервера]
      
      [Peer]
      PublicKey = [публичный ключ клиента]
      AllowedIPs = 10.0.0.2/32
      
      [Peer]
      PublicKey = [публичный ключ клиента]
      AllowedIPs = 10.0.0.3/32
   ```
   4. Далее настроен VPN клиент на 2-м CHR:

   Добавлен интерфейс:
   
   ![image](https://github.com/oxxawsm/2023_2024-network_programming-k34212-titenko_e_v/assets/63160594/f890bfa5-9daf-4e1a-89db-516ec230e831)

   Добавлен пир:
   
   ![image](https://github.com/oxxawsm/2023_2024-network_programming-k34212-titenko_e_v/assets/63160594/33e3faa8-dde1-4695-974b-216176a35777)

   Настроен IP:

   ![image](https://github.com/oxxawsm/2023_2024-network_programming-k34212-titenko_e_v/assets/63160594/67ad8b87-ca28-4c7f-bed8-1309d5ed0b59)

   5. На сервере перезапущен Wireguard:

   ```systemctl restart wg-quick@wg0```

### Проверка доступности CHR-ов:
   
   ![image](https://github.com/oxxawsm/2023_2024-network_programming-k34212-titenko_e_v/assets/63160594/19064f1d-a883-49b3-8791-02a41961a4ce)

### Проверка доступности сервера (на обоих клиентах работает):
   
   ![image](https://github.com/oxxawsm/2023_2024-network_programming-k34212-titenko_e_v/assets/63160594/968a1038-79bf-4792-81ac-1724295edffb)

### Настройка CHR-ов с помощью Ansible:

   Создан файл /etc/ansible/hosts.yml, в котором указана информация о CHR1 и CHR2 - их и будем настраивать, а также общие переменные:
   
   ![image](https://github.com/oxxawsm/2023_2024-network_programming-k34212-titenko_e_v/assets/63160594/f2ac8df6-e421-4e08-a8b9-ecddd5d9212e)

   Проверен inventory:

   ![image](https://github.com/oxxawsm/2023_2024-network_programming-k34212-titenko_e_v/assets/63160594/61faa944-039f-4e7a-93b5-4d17d0372b9f)

   Для дальнейшей настройки необходимо настроить ssh-соединения с ВМ в Я.Облаке и роутерами. Ждя этого сгенерированы ключи, публичные ключи скопированы на роутеры:
   
    ```
    ssh-keygen
    ssh admin@10.0.0.2 "/file print file=mykey; file set mykey contents=\"`cat ~/.ssh/id_rsa.pub`\";/user ssh-keys import public-key-file=mykey.txt;/ip ssh set always-allow-password-login=yes"
    ssh admin@10.0.0.3 "/file print file=mykey; file set mykey contents=\"`cat ~/.ssh/id_rsa.pub`\";/user ssh-keys import public-key-file=mykey.txt;/ip ssh set always-allow-password-login=yes"
    ```
    
  CHR-ы доступны:

  ![image](https://github.com/oxxawsm/2023_2024-network_programming-k34212-titenko_e_v/assets/63160594/7ae4425f-d450-4eb8-8285-fc1db180af14)

  Далее создан файл playbook.yaml - он будет содержать инструкции для выполнения задач на роутерах: 
  
  ```touch /etc/ansible/playbook.yml```

  По заданию нужно настроить креды для входа, NTP, OSPF с указанием Router ID, а также собрать данные по OSPF топологии и полный конфиг устройств. 
  
  Поэтому playbook.yaml должен выглядеть следующим образом (файл также лежит в репозитории выше в директории files):

  ![image](https://github.com/oxxawsm/2023_2024-network_programming-k34212-titenko_e_v/assets/63160594/cf29515c-880b-4300-905f-22c84239fb7c)

  Далее запущен playbook:

  ![image](https://github.com/oxxawsm/2023_2024-network_programming-k34212-titenko_e_v/assets/63160594/8b07f7a8-019b-4c7d-8ef4-787c1411e578)

  ![image](https://github.com/oxxawsm/2023_2024-network_programming-k34212-titenko_e_v/assets/63160594/722aaa00-5fc8-416b-8b59-20fdd29c910e)


### Проверка настройки роутеров CHR

Проверена работа OSPF на CHR1:

![image](https://github.com/oxxawsm/2023_2024-network_programming-k34212-titenko_e_v/assets/63160594/f2528836-eedc-4aa4-a6b5-59eeab64f72a)

И на CHR2:

![image](https://github.com/oxxawsm/2023_2024-network_programming-k34212-titenko_e_v/assets/63160594/b8b2d7b5-0c4d-46c6-a27f-ac1a1076fe30)

Далее проверена связность между роутерами:

![image](https://github.com/oxxawsm/2023_2024-network_programming-k34212-titenko_e_v/assets/63160594/71b2ea41-7664-4f87-beb5-a60940789eb9)

![image](https://github.com/oxxawsm/2023_2024-network_programming-k34212-titenko_e_v/assets/63160594/28265b39-af0b-4cfd-8afb-5d214e480c6f)

### Схема связи


### Вывод
В ходе выполнения лабораторной работы c помощью Ansible настроены 2 сетевых устройства: логин\пароль для входа, NTP, OSPF с указанием Router ID, собрана информацию о них. Правильно собран файл Inventory. Успешно установлена связь между всеми сетевыми устройствами.


