# Домашнее задание к занятию «Установка Kubernetes»

### Цель задания

Установить кластер K8s.

### Чеклист готовности к домашнему заданию

1. Развёрнутые ВМ с ОС Ubuntu 20.04-lts.


### Инструменты и дополнительные материалы, которые пригодятся для выполнения задания

1. [Инструкция по установке kubeadm](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/create-cluster-kubeadm/).
2. [Документация kubespray](https://kubespray.io/).
3. [Инструкция по которой настраивал](https://clo.ru/help/containerization/installation/rke2)
4. [Инструкция по keepliveed](https://github.com/BigKAA/youtube/blob/master/kubeadm/ha_cluster.md)
5. [Инструкция по настройке keepliveed в НС](https://yandex.cloud/ru/docs/tutorials/routing/bms-vrf-routing)
-----

### Задание 1. Установить кластер k8s с 1 master node

1. Подготовка работы кластера из 5 нод: 1 мастер и 4 рабочие ноды.
2. В качестве CRI — containerd.
3. Запуск etcd производить на мастере.
4. Способ установки выбрать самостоятельно.

-----
### Ответ 
1.  С помощью terraform развернем в облаке 3 сервера для мастер нод и 4 сервера для рабочих нод
![image](https://github.com/user-attachments/assets/4839fc55-ebc2-4bcf-982d-2c48b387929c)

![image](https://github.com/user-attachments/assets/dca6b8bc-2934-4b0d-813e-27b0fae0adef)
Установим RKE на мастере
![image](https://github.com/user-attachments/assets/41b42327-886b-4fc6-ac6d-1b9393811514)
Выполним команды на мастере
![image](https://github.com/user-attachments/assets/bc29f053-8f3e-4e10-9379-73d5a80694f0)
Выполним команды на рабочих нодах
![image](https://github.com/user-attachments/assets/d4835057-f914-4c19-9ad2-f5b6bfbae363)

Проверим статус на мастере

![image](https://github.com/user-attachments/assets/e000be43-db0e-4245-ae93-afa36bbb33a6)

Запустим под на кластере. 
![image](https://github.com/user-attachments/assets/17ebedc9-8ba4-42ed-8181-1774787d3328)
![image](https://github.com/user-attachments/assets/9940c168-9986-421c-ab0a-a6629419f1d8)


-----
   

## Дополнительные задания (со звёздочкой)

**Настоятельно рекомендуем выполнять все задания под звёздочкой.** Их выполнение поможет глубже разобраться в материале.   
Задания под звёздочкой необязательные к выполнению и не повлияют на получение зачёта по этому домашнему заданию. 

------
### Задание 2*. Установить HA кластер

1. Установить кластер в режиме HA.
2. Использовать нечётное количество Master-node.
3. Для cluster ip использовать keepalived или другой способ.

-----
### Ответ 
1. Добавим к нашему кластеру мастер ноды
   
![image](https://github.com/user-attachments/assets/13ce5317-5c26-4a94-8820-988294893ecd)

![image](https://github.com/user-attachments/assets/184936c3-b57e-4bd6-bcd9-5176268269b9)

![image](https://github.com/user-attachments/assets/d1ef71bc-a497-4d07-bdf4-b21b80c009c9)

Остановим 1 ноду

![image](https://github.com/user-attachments/assets/8eedfd26-d567-4d5d-8bfd-d9fb8654f110)

Проверим доступность пода

![image](https://github.com/user-attachments/assets/76471127-ae91-4357-8ab1-3fefbfb0acaa)

Настроим loadBalancer YC
![image](https://github.com/user-attachments/assets/35443f5b-784f-4d32-8838-bf7074ccf761)

Проверим доступность нашего пода по новому виртуальному IP
![image](https://github.com/user-attachments/assets/d482a67a-ca6a-4e18-b36e-ebc0132499b9)





-----
