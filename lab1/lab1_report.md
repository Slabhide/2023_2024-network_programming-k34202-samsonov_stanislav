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
Развертывание виртуальной машины на базе платформы Yandex Cloud с установленной системой контроля конфигураций Ansible и установка CHR в VirtualBox.

# Ход работы
### 1. Создание и подготовка виртуальной машины в Yandex Cloud.
В качестве облачного провайдера выбран YandexCloud. Была создана виртуальная машина и выполнено подключение по SSH.
Для дальнейшего выполнения лабораторной необходимо установить Ansible и желательно обновить ПО, для этого в терминале вводятся команды:
```
sudo apt update
sudo apt install python3-pip
sudo pip3 install ansible
```
![3](https://github.com/Slabhide/2023_2024-network_programming-k34202-samsonov_stanislav/blob/main/lab1/pictures/3.png)
Приготовления к настройке завершены.

### 2. Установка CHR в VirtualBox

Образ скачивается с сайта https://mikrotik.com/download из раздела **Cloud Hosted Router** в формате **vdi**. В VirtualBox при создании машины указывается тип - Other, версия Other/Unknown (64-bit), а в качестве хранилища указывается ранее скаченный файл в формате *.vdi*. В настройке ВМ в одном из адаптеров указывается тип подключения: *Сетевой мост*, тип адаптера *Intel PRO/1000 MT Desktop (82540EM)*, неразборчивый режим - *Разрешить всё* и обновляется MAC-адрес.
  
 После запуска в качестве логина вводится *admin* и задается новый пароль. На этом предварительная настройка завершена.
 [2]
Далее был определен ip-адрес Microtic для работы с WinBox.
[4]
В WinBox подключаемся к адресу для дальнейшей настройки
[5]
### 3. Поднятие VPN туннеля.
Создаем OpenVPN сервер на облачной виртуальной машине.
```
apt update && apt -y install ca-certificates wget net-tools gnupg
wget https://as-repository.openvpn.net/as-repo-public.asc -qO /etc/apt/trusted.gpg.d/as-repository.asc
echo "deb [arch=amd64 signed-by=/etc/apt/trusted.gpg.d/as-repository.asc] http://as-repository.openvpn.net/as/debian jammy main">/etc/apt/sources.list.d/openvpn-as-repo.list
apt update && apt -y install openvpn-as
```
[6]
Для того чтобы создать клиента и изменить конфигурацию OpenVPN-сервера, был использован Admin UI (https://<публичный ip>:943/admin).
[7]
В настройках VPN отключаем TLS.
[8]
В User Permitions создаем нового пользователя и разрешаем автоматический вход.
[9]
Во вкладке User Profiles нажимаем New Profile и начинается загрузка .ovpn файла.
[10]
Подробный статус о настроенном сервере
[11]

### 4. Подключение CHR.

Был выполнен переход в WinBox в Files и добвлен скаченный файл конфигурации.
Через терминал импортируем сертификаты из файла, используя команду <code>certificate import file-name=*название_файла*</code>.
[13]
Далее был создан новый интерфейс.
[12]
После чего была выполнена проверка
[14]


### Вывод: в ходе выполнения лабораторной работы был получен базовый опыт настройки VPN, используя OpenVPN, и работы с облачным провайдером на примере YandexCloud.







