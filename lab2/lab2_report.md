University: [ITMO University](https://itmo.ru/ru/) <br/>
Faculty: [FICT](https://fict.itmo.ru) <br/>
Course: [Network programming](https://github.com/itmo-ict-faculty/network-programming) <br/>
Year: 2023/2024 <br/>
Group: K34202 <br/>
Author: Samsonov Stanislav <br/>
Lab: Lab1 <br/>
Date of create: 2023 <br/>
Date of finished: 2023 <br/>


# Цель работы
С помощью Ansible настроить несколько сетевых устройств и собрать информацию о них. Правильно собрать файл Inventory.
## Ход работы:
   
   ### 1. Установка второго CHR###
   
Была создана виртуальная машина CHR_2 на VirtualBox. Был получен IP-адрес ВМ.
![1](https://github.com/Slabhide/2023_2024-network_programming-k34202-samsonov_stanislav/blob/main/lab2/pictures/1.png)
      
   ### 2. Настройка CHR-машин с помощью Ansible

Был настроен файл инвентаризации /etc/ansible/host.
      
      ```
      ansible-inventory --list -y
      ```
![2](https://github.com/Slabhide/2023_2024-network_programming-k34202-samsonov_stanislav/blob/main/lab2/pictures/2.png)
Произведена проверка подключения.
   
      ```
      ansible all -m ping
      ```
![3](https://github.com/Slabhide/2023_2024-network_programming-k34202-samsonov_stanislav/blob/main/lab2/pictures/3.png)
    
С помощью Ansible был создан пользователm user на роутерах. 
![4](https://github.com/Slabhide/2023_2024-network_programming-k34202-samsonov_stanislav/blob/main/lab2/pictures/4.png)
![5](https://github.com/Slabhide/2023_2024-network_programming-k34202-samsonov_stanislav/blob/main/lab2/pictures/5.png)
      
На роутерах был настроен NTP-клиент
![6](https://github.com/Slabhide/2023_2024-network_programming-k34202-samsonov_stanislav/blob/main/lab2/pictures/6.png)
   
Был настроен OSPF с помощью Ansible. В разделе Neighbor в Winbox была выполнена проверка правильности настройки OSPF.
![7](https://github.com/Slabhide/2023_2024-network_programming-k34202-samsonov_stanislav/blob/main/lab2/pictures/7.png)

Конфигурации роутеров были сохранены в файлах router.rsc, router2.rsc.
![8](https://github.com/Slabhide/2023_2024-network_programming-k34202-samsonov_stanislav/blob/main/lab2/pictures/8.png)


## Вывод: 
   Ansible мощный инструмент для множественного конфигурирования устройств и сбора информации с устройств.
