# Домашнее задание к занятию «Сетевое взаимодействие в K8S. Часть 1»

### Цель задания

В тестовой среде Kubernetes необходимо обеспечить доступ к приложению, установленному в предыдущем ДЗ и состоящему из двух контейнеров, по разным портам в разные контейнеры как внутри кластера, так и снаружи.

### Инструменты и дополнительные материалы, которые пригодятся для выполнения задания

1. [Описание](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/) Deployment и примеры манифестов.
2. [Описание](https://kubernetes.io/docs/concepts/services-networking/service/) Описание Service.
3. [Описание](https://github.com/wbitt/Network-MultiTool) Multitool.


------

### Задание 1. Создать Deployment и обеспечить доступ к контейнерам приложения по разным портам из другого Pod внутри кластера

1. Создать Deployment приложения, состоящего из двух контейнеров (nginx и multitool), с количеством реплик 3 шт.
2. Создать Service, который обеспечит доступ внутри кластера до контейнеров приложения из п.1 по порту 9001 — nginx 80, по 9002 — multitool 8080.
3. Создать отдельный Pod с приложением multitool и убедиться с помощью `curl`, что из пода есть доступ до приложения из п.1 по разным портам в разные контейнеры.
4. Продемонстрировать доступ с помощью `curl` по доменному имени сервиса.
5. Предоставить манифесты Deployment и Service в решении, а также скриншоты или вывод команды п.4.

-----
### Ответ
1. Созданы Deployment и сервис для него  состоящий из 2 контейнеров в количестве 3 реплик
   
                        apiVersion: v1
                        kind: Service
                        metadata:
                          name: netology-svc-multiport
                          namespace: task4
                        spec:
                          ports:
                            - name: http-nginx
                              port: 9001
                              targetPort: nginx-port
                            - name: http-multitool
                              port: 9002
                              targetPort: multitool-port
                          selector:
                            app: netology04
                          type: ClusterIP
                            ----
                        apiVersion: apps/v1
                        kind: Deployment
                        metadata:
                          name: netology-dep-multiport
                          labels:
                           app: netology04
                          namespace: task4
                        spec:
                         selector:
                          matchLabels:
                           app: netology04
                         replicas: 3
                         template :
                          metadata:
                           labels:
                            app: netology04
                          spec:
                           containers:
                            - name: nginx
                              image: nginx:latest
                              ports:
                              - containerPort: 80
                                name: nginx-port
                            - name: multitool
                              image: wbitt/network-multitool:latest
                              ports:
                              - containerPort: 8080
                                name: multitool-port 
                              env:
                              - name: HTTP_PORT
                                value: "8080"

![image](https://github.com/user-attachments/assets/b34c9c8d-bf2c-4b3d-8e2c-c43d8718f611)

2. Создадаим с помощью команды контейнер curl  и проверим ответы сервиса
   
![image](https://github.com/user-attachments/assets/6d949150-50d2-4498-a1c4-f80def9be72a)

![image](https://github.com/user-attachments/assets/b03d1f67-7239-4185-91b3-27fe1513800f)
   
------

### Задание 2. Создать Service и обеспечить доступ к приложениям снаружи кластера

1. Создать отдельный Service приложения из Задания 1 с возможностью доступа снаружи кластера к nginx, используя тип NodePort.
2. Продемонстрировать доступ с помощью браузера или `curl` с локального компьютера.
3. Предоставить манифест и Service в решении, а также скриншоты или вывод команды п.2.

------
### Ответ
1. Создадим сервис с типом NodePort для доступа к подам с наружи кластера

![image](https://github.com/user-attachments/assets/6b882082-e11f-4f36-a243-716060e16ece)

2. Проверим доступность сервисов в браузере 
![image](https://github.com/user-attachments/assets/463dbcbb-d2cc-4dae-881e-6793aeee7fde)

3. Проверим доступность curl

![image](https://github.com/user-attachments/assets/dd19562a-8e6b-4947-9b8f-a16e1ffb70a2)


------
