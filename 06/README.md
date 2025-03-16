# Домашнее задание к занятию «Хранение в K8s. Часть 1»

### Цель задания

В тестовой среде Kubernetes нужно обеспечить обмен файлами между контейнерам пода и доступ к логам ноды.

### Дополнительные материалы для выполнения задания

1. [Инструкция по установке MicroK8S](https://microk8s.io/docs/getting-started).
2. [Описание Volumes](https://kubernetes.io/docs/concepts/storage/volumes/).
3. [Описание Multitool](https://github.com/wbitt/Network-MultiTool).

------

### Задание 1 

**Что нужно сделать**

Создать Deployment приложения, состоящего из двух контейнеров и обменивающихся данными.

1. Создать Deployment приложения, состоящего из контейнеров busybox и multitool.
2. Сделать так, чтобы busybox писал каждые пять секунд в некий файл в общей директории.
3. Обеспечить возможность чтения файла контейнером multitool.
4. Продемонстрировать, что multitool может читать файл, который периодоически обновляется.
5. Предоставить манифесты Deployment в решении, а также скриншоты или вывод команды из п. 4.

------
### Ответ
1. Создадим Deployment с двумя контейнерами и примонтируем им один и тот же volume
   Напишем в контейнере busybox команду для записи в фаил данных каждый 5 секунд

                       apiVersion: apps/v1
                      kind: Deployment
                      metadata:
                        name: netology-dep-multiport
                        labels:
                         app: netology04
                      spec:
                        selector:
                          matchLabels:
                            app: netology04
                        replicas: 1
                        template :
                          metadata:
                            labels:
                              app: netology04
                          spec:
                            containers:
                            - name: busybox
                              image: busybox
                              command: ['sh', '-c', 'while true; do echo $(date) - Check work volume >> /tmp/cache/check.txt; sleep 5; done']
                              volumeMounts:
                              - mountPath: "/tmp/cache"
                                name: my-volume
                            - name: multitool
                              image: wbitt/network-multitool:latest
                              ports:
                              - containerPort: 8080
                                name: multitool-port 
                              env:
                              - name: HTTP_PORT
                                value: "8080"
                              volumeMounts:
                              - mountPath: "/tmp/cache"
                                name: my-volume
                            volumes:
                            - name: my-volume
                              emptyDir: {}

2. подключимся к обоим сонтенерам и проверим фаил с обоих
![image](https://github.com/user-attachments/assets/4c0ee283-2540-488a-945e-7bb37f075c3b)
 

------

### Задание 2

**Что нужно сделать**

Создать DaemonSet приложения, которое может прочитать логи ноды.

1. Создать DaemonSet приложения, состоящего из multitool.
2. Обеспечить возможность чтения файла `/var/log/syslog` кластера MicroK8S.
3. Продемонстрировать возможность чтения файла изнутри пода.
4. Предоставить манифесты Deployment, а также скриншоты или вывод команды из п. 2.

------
### Ответ
1. создадим DaemonSet приложения.
   Настроим подключение контейнера к /var/log/syslog нод кластера

                            apiVersion: apps/v1
                            kind: DaemonSet
                            metadata:
                              name: demonset-check-log
                              labels:
                                k8s-app: netolohy06
                            spec:
                              selector:
                                matchLabels:
                                  name: netolohy06
                              template:
                                metadata:
                                  labels:
                                    name: netolohy06
                                spec:
                                  containers:
                                  - name: multitool
                                    image: wbitt/network-multitool:latest
                                    ports:
                                    - containerPort: 8080
                                      name: backend-port 
                                    env:
                                    - name: HTTP_PORT
                                      value: "8080"
                                    volumeMounts:
                                    - name: varlog
                                      mountPath: /log_cluster
                                  # it may be desirable to set a high priority class to ensure that a DaemonSet Pod
                                  # preempts running Pods
                                  # priorityClassName: important
                                  terminationGracePeriodSeconds: 30
                                  volumes:
                                  - name: varlog
                                    hostPath:
                                      path: /var/log
   
3. Запустим под и проверим подключение файлов
   ![image](https://github.com/user-attachments/assets/455232b1-a9e0-48ec-bba8-e3155de0e0e9)

------
