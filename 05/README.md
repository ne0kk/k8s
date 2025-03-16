# Домашнее задание к занятию «Сетевое взаимодействие в K8S. Часть 2»

### Цель задания

В тестовой среде Kubernetes необходимо обеспечить доступ к двум приложениям снаружи кластера по разным путям.

------

### Инструменты и дополнительные материалы, которые пригодятся для выполнения задания

1. [Инструкция](https://microk8s.io/docs/getting-started) по установке MicroK8S.
2. [Описание](https://kubernetes.io/docs/concepts/services-networking/service/) Service.
3. [Описание](https://kubernetes.io/docs/concepts/services-networking/ingress/) Ingress.
4. [Описание](https://github.com/wbitt/Network-MultiTool) Multitool.

------

### Задание 1. Создать Deployment приложений backend и frontend

1. Создать Deployment приложения _frontend_ из образа nginx с количеством реплик 3 шт.
2. Создать Deployment приложения _backend_ из образа multitool. 
3. Добавить Service, которые обеспечат доступ к обоим приложениям внутри кластера. 
4. Продемонстрировать, что приложения видят друг друга с помощью Service.
5. Предоставить манифесты Deployment и Service в решении, а также скриншоты или вывод команды п.4.

------
### Ответ
1. Создадим Deployment _frontend_

              apiVersion: apps/v1
              kind: Deployment
              metadata:
                name: netology-dep-frontend
                labels:
                 app: netology05
                namespace: task4
              spec: 
               selector:
                matchLabels:
                 app: netology05
               replicas: 3
               template :
                metadata:
                 labels:
                  app: netology05
                spec:
                 containers:
                  - name: nginx
                    image: nginx:latest
                    ports:
                    - containerPort: 80
                      name: front-port
   
2. Создадим Deployment _backend_


                    apiVersion: apps/v1
                    kind: Deployment
                    metadata:
                      name: netology-dep-backend
                      labels:
                       app: netology05
                      namespace: task4
                    spec:
                     selector:
                      matchLabels:
                       app: netology05
                     replicas: 1
                     template :
                      metadata:
                       labels:
                        app: netology05
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

   
3. Добавим Service, как оказалось, по заданию, необходимо создать два разных сервиса, что бы настроить разные ответы по одному порту 80 по одному IP

                        apiVersion: v1
                        kind: Service
                        metadata:
                          name: svc-front
                          namespace: task4
                        spec:
                          ports:
                            - name: http-nginx
                              port: 80
                              targetPort: front-port
                          selector:
                            app: netology05
                          type: ClusterIP
                        
                        apiVersion: v1
                        kind: Service
                        metadata:
                          name: svc-back
                          namespace: task4
                        spec:
                          ports:
                            - name: http-multitool
                              port: 80
                              targetPort: backend-port
                          selector:
                            app: netology05
                          type: ClusterIP
  
   
4. Проверим доступность приложения
![image](https://github.com/user-attachments/assets/cba5735e-a5c5-4cc1-8a94-ae1903421106)
![image](https://github.com/user-attachments/assets/57d7867f-15bc-4662-ab8f-ca6db19d828e)

------

### Задание 2. Создать Ingress и обеспечить доступ к приложениям снаружи кластера

1. Включить Ingress-controller в MicroK8S.
2. Создать Ingress, обеспечивающий доступ снаружи по IP-адресу кластера MicroK8S так, чтобы при запросе только по адресу открывался _frontend_ а при добавлении /api - _backend_.
3. Продемонстрировать доступ с помощью браузера или `curl` с локального компьютера.
4. Предоставить манифесты и скриншоты или вывод команды п.2.

------
### Ответ
1. Включим ingress-controller на ноде кластера MicroK8S

   ![image](https://github.com/user-attachments/assets/b76dfd56-98fe-4c79-b30d-76f0b9b7ccc1)

2. создадим ingress-controller

                    apiVersion: networking.k8s.io/v1
                    kind: Ingress
                    metadata:
                      name: ingress
                      namespace: task4
                      annotations:
                        nginx.ingress.kubernetes.io/rewrite-target: /
                      
                    spec:
                      ingressClassName: public
                      rules:
                      - host: netology-ingress.ru
                        http:
                          paths:
                          - path: /
                            pathType: Prefix
                            backend:
                              service:
                                name: svc-front
                                port:
                                  number: 80
                          - path: /api
                            pathType: Prefix
                            backend:
                              service:
                                name: svc-back
                                port:
                                  number: 80

3.  пропишем адрес кластера в локальном hosts
![image](https://github.com/user-attachments/assets/d737f54c-dada-4364-b872-24ebf9b283c7)

4. Проверим доступность приложения
![image](https://github.com/user-attachments/assets/83bab1a6-79f1-4a5f-926f-5eabc7de97df)
![image](https://github.com/user-attachments/assets/1f3c63f7-7a06-4410-a89b-b73eba97aff5)

   
------
