# Домашнее задание к занятию «Хранение в K8s. Часть 2»

### Цель задания

В тестовой среде Kubernetes нужно создать PV и продемострировать запись и хранение файлов.

------

### Дополнительные материалы для выполнения задания

1. [Инструкция по установке NFS в MicroK8S](https://microk8s.io/docs/nfs). 
2. [Описание Persistent Volumes](https://kubernetes.io/docs/concepts/storage/persistent-volumes/). 
3. [Описание динамического провижининга](https://kubernetes.io/docs/concepts/storage/dynamic-provisioning/). 
4. [Описание Multitool](https://github.com/wbitt/Network-MultiTool).

------

### Задание 1

**Что нужно сделать**

Создать Deployment приложения, использующего локальный PV, созданный вручную.

1. Создать Deployment приложения, состоящего из контейнеров busybox и multitool.
2. Создать PV и PVC для подключения папки на локальной ноде, которая будет использована в поде.
3. Продемонстрировать, что multitool может читать файл, в который busybox пишет каждые пять секунд в общей директории. 
4. Удалить Deployment и PVC. Продемонстрировать, что после этого произошло с PV. Пояснить, почему.
5. Продемонстрировать, что файл сохранился на локальном диске ноды. Удалить PV.  Продемонстрировать что произошло с файлом после удаления PV. Пояснить, почему.
5. Предоставить манифесты, а также скриншоты или вывод необходимых команд.

------
### Ответ 
1. Создадим Deployment приложения, состоящего из контейнеров busybox и multitool
Создадим PV и PVC 

                      apiVersion: apps/v1
                      kind: Deployment
                      metadata:
                        name: netology-dep-pv
                        namespace: task7
                        labels:
                         app: netology07
                      spec:
                        selector:
                          matchLabels:
                            app: netology07
                        replicas: 1
                        template :
                          metadata:
                            labels:
                              app: netology07
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
                              persistentVolumeClaim:
                                claimName: pvc-manual
                              
                      ---
                      apiVersion: v1
                      kind: PersistentVolumeClaim
                      metadata:
                        name: pvc-manual
                        namespace: task7
                      spec:
                        storageClassName: ""
                        accessModes:
                          - ReadWriteOnce
                        resources:
                          requests:
                            storage: 1Mi
                      ---
                      apiVersion: v1
                      kind: PersistentVolume
                      metadata:
                        name: pv-manual
                      spec:
                        storageClassName: ""
                        accessModes:
                          - ReadWriteOnce
                        capacity:
                          storage: 1Mi
                        hostPath:
                          path: /data/pv
                        persistentVolumeReclaimPolicy: Delete
   
![image](https://github.com/user-attachments/assets/b2f0a8ac-7171-44fd-89bd-a9a6833caa77)


2. multitool может читать фаил который пищет busybox
   
![image](https://github.com/user-attachments/assets/b6e5e002-ac18-499a-aa41-bf3fab738e00)

3. удалим Deployment и PVC.
   
![image](https://github.com/user-attachments/assets/e9ece2a4-c547-4320-b217-4afd7b3e081a)

PV в статусе Failed.
Это связано с тем что PV не видит более ни одного PVC привязанного в нему. 
На ноде фаил не удлен. 

![image](https://github.com/user-attachments/assets/afcce27c-1f1a-4c09-91fc-f5b93498f06d)

4. удалим PV 
Даже имея ключ  persistentVolumeReclaimPolicy: Delete фаил с данными всеравно не удаляется с ноды , так как драйвер не может применить нужный метод к локальному хранилищу.

![image](https://github.com/user-attachments/assets/492713b8-69d0-465e-ad82-d37885c3ac4f)

------

### Задание 2

**Что нужно сделать**

Создать Deployment приложения, которое может хранить файлы на NFS с динамическим созданием PV.

1. Включить и настроить NFS-сервер на MicroK8S.
2. Создать Deployment приложения состоящего из multitool, и подключить к нему PV, созданный автоматически на сервере NFS.
3. Продемонстрировать возможность чтения и записи файла изнутри пода. 
4. Предоставить манифесты, а также скриншоты или вывод необходимых команд.

------
### Ответ 
1. Включим на ноде storage для настройки nfs
   
![image](https://github.com/user-attachments/assets/2d07de17-776b-4b5f-86d8-3ad728cb7979)

2. создадим Deployment с контейнером multitool, и выберем storageclasses в PVC.

![image](https://github.com/user-attachments/assets/0efe87c8-fed8-43bf-9a3d-d9bfe9992839)

                      apiVersion: apps/v1
                      kind: Deployment
                      metadata:
                        name: netology-dep-pv
                        namespace: task7
                        labels:
                         app: netology07
                      spec:
                        selector:
                          matchLabels:
                            app: netology07
                        replicas: 1
                        template :
                          metadata:
                            labels:
                              app: netology07
                          spec:
                            containers:
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
                              persistentVolumeClaim:
                                claimName: pvc-manual
                              
                      ---
                      apiVersion: v1
                      kind: PersistentVolumeClaim
                      metadata:
                        name: pvc-manual
                        namespace: task7
                      spec:
                        storageClassName: "microk8s-hostpath"
                        accessModes:
                          - ReadWriteOnce
                        resources:
                          requests:
                            storage: 1Mi

Проверим работу пода  и создание PV

![image](https://github.com/user-attachments/assets/a92a605e-a5f4-4a9e-b856-5c834dd8c536)

3. проверим возможность чтения и записи внутри пода

![image](https://github.com/user-attachments/assets/1ee68c53-4680-4587-9c8a-ba83e83353b9)

Внутри ноды 

![image](https://github.com/user-attachments/assets/72fa1630-5c0d-41dc-a9a0-f38bb459f37b)



------
