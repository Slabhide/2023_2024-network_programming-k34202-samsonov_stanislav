University: [ITMO University](https://itmo.ru/ru/) <br/>
Faculty: [FICT](https://fict.itmo.ru) <br/>
Course: [Network programming](https://github.com/itmo-ict-faculty/network-programming) <br/>
Year: 2023/2024 <br/>
Group: K34202 <br/>
Author: Samsonov Stanislav <br/>
Lab: Lab3 <br/>
Date of create: 2023 <br/>
Date of finished: 2023 <br/>


# Цель работы
С помощью Ansible и Netbox собрать всю возможную информацию об устройствах и сохранить в отдельном файле.
## Ход работы:
#### 1. Устанавливаем Netbox
Сначала установим postgresql и подключимся к нему. 
Также создадим базу данных с помощью команд 
![1]
Был установлен Redis.
```
sudo apt install -y redis-server
```
#### Установка Python
```
sudo apt install -y python3 python3-pip python3-venv python3-dev build-essential libxml2-dev libxslt1-dev libffi-dev libpq-dev libssl-dev zlib1g-dev
```
[4]
Был склонирован репозиторий с NetBox:
[2]
Далее был добавлен пользователь.
[3]
Был сгенерирован секретный ключ с помощью библиотеки python
[5]
В каталоге конфигурации NetBox создана копия configuration_example.py с именем configuration.py. Этот файл будет содержать все локальные параметры конфигурации.Был отредактирован файл configuration.py
![6]
Далее был создан суперпользователь.
![7]
Был запущен NetBox:
```
python3 manage.py runserver 0.0.0.0:8000 --insecure
```
[8]
### 2. Заполнение информации о CHR в Netbox.
Создан сайт, мануфактура и роль. Созданы 2 роутера.
[9]
Также получен токен
[11]
### 3. Сбор данных из Netbox используя Ansible.
Установлены ansible-модули для Netbox:
```
ansible-galaxy collection install netbox.netbox
```
[10]
Создан файл netbox_conf_galaxy.yml:
```
plugin: netbox.netbox.nb_inventory
api_endpoint: http://127.0.0.1:8000
token: 1rg8135rr424h628bdad25ty37e4ed531d37bf88d
validate_certs: True
config_context: False
interfaces: True
```
Сохранен вывод скрипта в файл командой:
```
ansible-inventory -v --list -y -i netbox_conf_galaxy.yml > netbox_inventory.yml
```
Теперь в этой файле находится информация об устройствах в YAML-формате.
### 4. Сценарий, при котором на основе данных из Netbox можно настроить 2 CHR, изменить имя устройства, добавить IP адрес на устройство.
Был отредактирован inventory-файл. Перенесены переменные для подключения к роутерам:
```
  vars:
    ansible_connection: ansible.netcommon.network_cli
    ansible_network_os: community.routeros.routeros
    ansible_user: admin
    ansible_ssh_pass: admin
```
Написан playbookдля изменения имени устройства и добавления IP:
```
- name: Setup Routers
  hosts: ungrouped
  tasks:
    - name: "Change names of devicies"
      community.routeros.command:
        commands:
          - /system identity set name="{{ interfaces[0].device.name }}"

    - name: "Change IP-address"
      community.routeros.command:
        commands:
          - /ip address add address="{{ interfaces[0].ip_addresses[0].address }}" interface="{{ interfaces[0].display }}"
```
### 5. Сценарий, позволяющий собрать серийный номер устройства и вносящий серийный номер в Netbox.
```
- name: Get Serial Numbers
  hosts: ungrouped
  tasks:

    - name: "Get Serial Number"
      community.routeros.command:
        commands:
          - /system license print
      register: license

    - name: "Get Name"
      community.routeros.command:
        commands:
          - /system identity print
      register: identity

    - name: Add Serial Number to Netbox
      netbox_device:
        netbox_url: http://127.0.0.1:8000
        netbox_token: 1ef9042ee515f716bdad25fe37e4ed531d38c097
        data:
          name: "{{ identity.stdout_lines[0][0].split()[1] }}"
          serial: "{{ license.stdout_lines[0][0].split()[1] }}"
```

Выполнение сценария:
```
ansible-playbook -i inventory serial_number-playbook.yml
```
Добавленные серийные номера:
![13]
![14]
### Вывод:

В результате выполнения работы c помощью Ansible и Netbox была собрана вся возможную информацию об устройствах и сохранена в отдельном файле.
