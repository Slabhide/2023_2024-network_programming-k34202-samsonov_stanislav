University: [ITMO University](https://itmo.ru/ru/) <br/>
Faculty: [FICT](https://fict.itmo.ru) <br/>
Course: [Network programming](https://github.com/itmo-ict-faculty/network-programming) <br/>
Year: 2023/2024 <br/>
Group: K34202 <br/>
Author: Samsonov Stanislav <br/>
Lab: Lab4 <br/>
Date of create: 2023 <br/>
Date of finished: 2023 <br/>


# Цель работы
Изучить синтаксис языка программирования P4 и выполнить 2 обучающих задания от Open network foundation для ознакомления на практике с P4.
## Ход работы:
### Клонирование репозитория
Был склонирован репозиторий p4lang/tutorials. Были установлены Vagrant и VirtualBox. 
Был выполнен переход в папку vm-ubuntu-20.04. Создана виртуальная машина с помощью команды vagrant up.
![1]
![2]
### Implementing Basic Forwarding
В данном задании нужно было дополнить скрипт basic.p4, чтобы в сети заработала переадресация IP-пакетов.

Был выполнен вход под учетной записью p4/p4.

Была выполнена команда make run, а затем проверена доступность узлов.
![3]
Был обновлен парсер, который позволяет заполнять заголовки ethernet_t, ipv4_t.

```
parser MyParser(packet_in packet,
                out headers hdr,
                inout metadata meta,
                inout standard_metadata_t standard_metadata) {

    state start { transition parse; }

    state parse{
      packet.extract(hdr.ethernet);
      transition select(hdr.ethernet.etherType) {
        TYPE_IPV4: parse_ipv4;
        default: accept;
        }
    }
      
    state parse_ipv4{
      packet.extract(hdr.ipv4);
      transition accept;
    }
}
```

Было дополнено action ip4_forward: был определен выходной порт, обновлен адрес назначения пакета, обновлен источник отправления пакета, уменьшено значение TTL.
```
control MyIngress(inout headers hdr,
                  inout metadata meta,
                  inout standard_metadata_t standard_metadata) {
    action drop() {
        mark_to_drop(standard_metadata);
    }

    action ipv4_forward(macAddr_t dstAddr, egressSpec_t port) {
        standard_metadata.egress_spec= port; // Изменяем порт
        hdr.ethernet.srcAddr = hdr.ethernet.dstAddr; // Меняем адрес источника на свой
        hdr.ethernet.dstAddr = dstAddr; // Устанавливаем нового получателя
        hdr.ipv4.ttl = hdr.ipv4.ttl - 1; // Декрементируем TTL
    }

    table ipv4_lpm {
        key = {
            hdr.ipv4.dstAddr: lpm; // ключ таблицы
        }
        actions = {       // возможные действия
            ipv4_forward; 
            drop;
            NoAction;
        }
        size = 1024;
        default_action = NoAction();
    }

    apply {
        if(hdr.ipv4.isValid()){
          ipv4_lpm.apply();
        }
    }
```
Добавлен депарсер, который выбирает порядок вставки полей:

```
control MyDeparser(packet_out packet, in headers hdr) {
    apply {
        packet.emit(hdr.ethernet);
        packet.emit(hdr.ipv4);
    }
}
```
Была выполнена проверка скрипта.
![4]
![5]
### Implementing Basic Tunneling
Был изменен файл basic_tunnel.p4: 
Был обновлен парсер myParser, а именно, добавлена функция заполненения заголовка myTunnel.
```
parser MyParser(packet_in packet,
                out headers hdr,
                inout metadata meta,
                inout standard_metadata_t standard_metadata) {

    state start {
        packet.extract(hdr.ethernet);
        transition select(hdr.ethernet.etherType) {
			TYPE_MYTUNNEL: parse_myTunnel;
            TYPE_IPV4 : parse_ipv4;
        }
    }

    state parse_ipv4 {
        packet.extract(hdr.ipv4);
        transition accept;
    }

	  state parse_myTunnel {
        packet.extract(hdr.myTunnel);
        transition select(hdr.myTunnel.proto_id) {
            TYPE_IPV4: parse_ipv4;
        }
    }
	
}
```

Определено действие myTunnel_forward, которое назначает выходной порт.
```
// TODO: declare a new action: myTunnel_forward(egressSpec_t port)
	action myTunnel_forward(egressSpec_t port) {
        standard_metadata.egress_spec = port;
    }
```
Определена таблица myTunnel_exact.
```
 // TODO: declare a new table: myTunnel_exact
	table myTunnel_exact {
        key = {
            hdr.myTunnel.dst_id: exact;
        }
        actions = {
            myTunnel_forward;
            drop;
        }
        size = 1024;
        default_action = drop();
    }
```
Обновлено выражение apply, которое позволяет применять myTunnel_exact, если заголовок myTunell валидный.
```
apply {
        // TODO: Update control flow
        if (hdr.ipv4.isValid()) {
            ipv4_lpm.apply();
        }
```
Был обновлен MyDeparser
```
control MyDeparser(packet_out packet, in headers hdr) {
    apply {
        packet.emit(hdr.ethernet);
        // TODO: emit myTunnel header as well
		packet.emit(hdr.myTunnel);
        packet.emit(hdr.ipv4);
    }
}
```
Была проверена локальная связность.
![6]
Была выполнена проверка скрипта, с h1 был оправлен пакет на h2 без туннелирования.
![7]
Был отправлен пакет с h1 на h2 с туннелированием.
![8]
![9]
### Вывод

В результате выполнения работы был изучен синтаксис языка программирования P4 и выполнены 2 обучающих задания от Open network foundation для ознакомления на практике с P4.
