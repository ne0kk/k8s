# Домашнее задание к занятию «Конфигурация приложений»

### Цель задания

В тестовой среде Kubernetes необходимо создать конфигурацию и продемонстрировать работу приложения.

------

### Инструменты и дополнительные материалы, которые пригодятся для выполнения задания

1. [Описание](https://kubernetes.io/docs/concepts/configuration/secret/) Secret.
2. [Описание](https://kubernetes.io/docs/concepts/configuration/configmap/) ConfigMap.
3. [Описание](https://github.com/wbitt/Network-MultiTool) Multitool.

------


### Задание 1. Создать Deployment приложения и решить возникшую проблему с помощью ConfigMap. Добавить веб-страницу

1. Создать Deployment приложения, состоящего из контейнеров nginx и multitool.
2. Решить возникшую проблему с помощью ConfigMap.
3. Продемонстрировать, что pod стартовал и оба конейнера работают.
4. Сделать простую веб-страницу и подключить её к Nginx с помощью ConfigMap. Подключить Service и показать вывод curl или в браузере.
5. Предоставить манифесты, а также скриншоты или вывод необходимых команд.

------
### Ответ
1. Создадим Deployment приложения состоящего из контейнеров nginx и multitool.
   Добавим сразу ConfigMap:
   multitool-port - изменим дефолтный порт nginx в контейнере multitool
   nginx-site - добавим свой фаил index.html
   Создадим сервис для 80 порта пода.

                          apiVersion: v1
                          kind: ConfigMap
                          metadata:
                            name: multitool-port
                          data:
                            key1: "1180"
                          
                          ---
                          apiVersion: v1
                          kind: ConfigMap
                          metadata:
                            name: nginx-site
                          data:
                           index.html: | 
                            <html>
                            <body>
                              <h1>Test ConfogMaps</h1>
                            </html>
                            </body>  
                          
                          ---
                          apiVersion: apps/v1
                          kind: Deployment
                          metadata:
                            name: netology-home
                            labels:
                             app: netology
                          spec:
                           selector:
                            matchLabels:
                             app: netology
                           replicas: 1
                           template :
                            metadata:
                             labels:
                              app: netology
                            spec:
                             containers:
                              - name: nginx
                                image: nginx:latest
                                ports:
                                - containerPort: 80
                                volumeMounts:
                                - name: nginx-html
                                  mountPath: /usr/share/nginx/html
                              - name: multitool
                                image: wbitt/network-multitool:latest
                                ports:
                                - containerPort: 8080
                                env:
                                - name: HTTP_PORT
                                  valueFrom: 
                                    configMapKeyRef:
                                      name: multitool-port
                                      key: key1
                             volumes:
                              - name: nginx-html
                                configMap:
                                 name: nginx-site
                          
                          --- 
                          
                          apiVersion: v1
                          kind: Service
                          metadata:
                            name: svc-front
                          spec:
                            ports:
                              - name: http-nginx
                                port: 80
                                targetPort: 80
                            selector:
                              app: netology
                            type: ClusterIP

2. Выполним деплой.

  ![image](https://github.com/user-attachments/assets/cecdd0c7-2440-4b2b-8444-3f35e4790150)

3. Порверим доступность приложегния по сервису и ответ сервера

![image](https://github.com/user-attachments/assets/8ec393f6-58a3-440b-b248-82a434a3da1b)
   
   
------
### Задание 2. Создать приложение с вашей веб-страницей, доступной по HTTPS 

1. Создать Deployment приложения, состоящего из Nginx.
2. Создать собственную веб-страницу и подключить её как ConfigMap к приложению.
3. Выпустить самоподписной сертификат SSL. Создать Secret для использования сертификата.
4. Создать Ingress и необходимый Service, подключить к нему SSL в вид. Продемонстировать доступ к приложению по HTTPS. 
4. Предоставить манифесты, а также скриншоты или вывод необходимых команд.

------
### Ответ
1. Создадим ConfigMap для своей страницы.

                    apiVersion: v1
                    kind: ConfigMap
                    metadata:
                      name: nginx-site
                      namespace: task8
                    data:
                     index.html: | 
                      <html>
                      <body>
                        <h1>Test ConfogMaps</h1>
                      </html>
                      </body> 

   Cоздадим  Deployment приложения с Nginx

                      apiVersion: apps/v1
                      kind: Deployment
                      metadata:
                        name: netology-home
                        namespace: task8
                        labels:
                         app: netology
                      spec:
                       selector:
                        matchLabels:
                         app: netology
                       replicas: 1
                       template :
                        metadata:
                         labels:
                          app: netology
                        spec:
                         containers:
                          - name: nginx
                            image: nginx:latest
                            ports:
                            - containerPort: 80
                            volumeMounts:
                            - name: nginx-html
                              mountPath: /usr/share/nginx/html
                         volumes:
                          - name: nginx-html
                            configMap:
                             name: nginx-site

   Создадим сервис для работы ингресса.

                          apiVersion: v1
                          kind: Service
                          metadata:
                            name: svc-front
                            namespace: task8
                          spec:
                            ports:
                              - name: http-nginx
                                port: 80
                                targetPort: 80
                            selector:
                              app: netology
                            type: ClusterIP

   Создадим Ingress

                            apiVersion: networking.k8s.io/v1
                            kind: Ingress
                            metadata:
                              name: ingress
                              namespace: task8
                              annotations:
                                nginx.ingress.kubernetes.io/rewrite-target: /
                                nginx.ingress.kubernetes.io/ssl-redirect: "false"
                            spec:
                              ingressClassName: public
                              tls:
                                - hosts:
                                  - netology-ingress.ru
                                  secretName: nginx-tls
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

   Выпустим самоподписанный сертификат SSL

                                openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout tls.key -out tls.crt -subj "/CN=netology-ingress.ru/O=netology-ingress.ru"
                               

   Создадим Secret

                                 kubectl create secret tls nginx-tls --cert=tls.crt --key=tls.key

   Проверим работу 80 и 443 портов приложения. 

![image](https://github.com/user-attachments/assets/75435752-5b43-4a28-aa14-7d06aa121f58)


------
