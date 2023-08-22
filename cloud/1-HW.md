# Домашнее задание к занятию «Организация сети»

---
### Задание 1. Yandex Cloud 

**Что нужно сделать**

1. Создать пустую VPC. Выбрать зону.
2. Публичная подсеть.

 - Создать в VPC subnet с названием public, сетью 192.168.10.0/24.
 - Создать в этой подсети NAT-инстанс, присвоив ему адрес 192.168.10.254. В качестве image_id использовать fd80mrhj8fl2oe87o4e1.
 - Создать в этой публичной подсети виртуалку с публичным IP, подключиться к ней и убедиться, что есть доступ к интернету.
3. Приватная подсеть.
 - Создать в VPC subnet с названием private, сетью 192.168.20.0/24.
 - Создать route table. Добавить статический маршрут, направляющий весь исходящий трафик private сети в NAT-инстанс.
 - Создать в этой приватной подсети виртуалку с внутренним IP, подключиться к ней через виртуалку, созданную ранее, и убедиться, что есть доступ к интернету.

### Решение
[Terraform](https://github.com/omega-pasha/nat_VPC_yc/tree/main)

```
Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value: yes

yandex_vpc_network.example_network: Creating...
yandex_vpc_network.example_network: Creation complete after 3s [id=enpddj7dobhglontphbb]
yandex_vpc_subnet.public-subnet-a: Creating...
yandex_vpc_route_table.routing_table: Creating...
yandex_vpc_route_table.routing_table: Creation complete after 1s [id=enps1reiq0kp46abbbab]
yandex_vpc_subnet.private-subnet-a: Creating...
yandex_vpc_subnet.public-subnet-a: Creation complete after 2s [id=e9bae2rg4qkdbvpg7m0p]
yandex_compute_instance.public-instance[0]: Creating...
yandex_compute_instance.nat-instance[0]: Creating...
yandex_vpc_subnet.private-subnet-a: Creation complete after 2s [id=e9bgcrganqd0jh2l1n1u]
yandex_compute_instance.private-instance[0]: Creating...
yandex_compute_instance.nat-instance[0]: Still creating... [10s elapsed]
.
.
.
yandex_compute_instance.private-instance[0]: Still creating... [1m30s elapsed]
yandex_compute_instance.private-instance[0]: Creation complete after 1m30s [id=fhmgvo7t4rdqbtlj5i74]
yandex_compute_instance.public-instance[0]: Creation complete after 1m35s [id=fhmfuc2373ng0iermj1s]
yandex_compute_instance.nat-instance[0]: Still creating... [1m40s elapsed]
yandex_compute_instance.nat-instance[0]: Still creating... [1m50s elapsed]
yandex_compute_instance.nat-instance[0]: Still creating... [2m0s elapsed]
yandex_compute_instance.nat-instance[0]: Creation complete after 2m1s [id=fhmqan76k916qucloo3h]

Apply complete! Resources: 7 added, 0 changed, 0 destroyed.

Outputs:

nat-instance_external_ip_address = [
  "84.201.172.156",
]
private-instance_iternal_ip_address = [
  "192.168.20.29",
]
public-instance_external_ip_address = [
  "158.160.43.37",
]
public-instance_iternal_ip_address = [
  "192.168.10.29",
]
```
Созданные ресурсы
![](https://github.com/omega-pasha/kuber-homeworks/blob/main/cloud/2023-08-23_01-26-12.png)

Traceroute from public
![](https://github.com/omega-pasha/kuber-homeworks/blob/main/cloud/2023-08-23_01-10-15.png)

Traceroute from private
![](https://github.com/omega-pasha/kuber-homeworks/blob/main/cloud/2023-08-23_01-11-53%202.png)

Видно, что чтобы попасть на ya.ru, маршрут сперва проходит через nat-instance
```
 1  192.168.20.1 (192.168.20.1)  0.422 ms  0.398 ms  0.383 ms
 2  * * *
 3  nat-instance-0.ru-central1.internal (192.168.10.254)  0.894 ms  0.910 ms  0.891 ms
 4  nat-instance-0.ru-central1.internal (192.168.10.254)  0.866 ms  0.847 ms  0.828 ms
```

